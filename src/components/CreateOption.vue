<template>
  <div class="modal-container" @click="closeModal">
    <div class="modal">
      <div class="close-btn">X</div>
      <h1>Create New Option for {{ info.dotDigitalAccount.accountName }}</h1>
      <div class="form">
        <div class="form-field">
          <input
            type="text"
            id="inp"
            v-model="platformName"
            placeholder="Platform Name"
          />
        </div>
        <div class="form-field">
          <input
            type="text"
            id="inp"
            v-model="optionName"
            placeholder="Option Name"
          />
        </div>
        <div class="form-field">
          <input
            type="text"
            id="inp"
            v-model="optionValue"
            placeholder="Option Value"
          />
        </div>
      </div>
      <button class="active" @click="createOption">CREATE NEW OPTION</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "CreateOption",
  props: ["info"],
  created() {},
  data() {
    return {
      optionName: "",
      optionValue: "",
      platformName: "",
      user: "yarden",
    };
  },
  methods: {
    createOption: async function () {
      let data = Object.assign({}, this.info);
      //deep copy of prop json
      if (!this.optionName || !this.optionValue || !this.platformName) {
        this.$swal({
          icon: "error",
          title: "Error",
          text: "Please make sure all the fields are filled properly",
        });
        return false;
      } else {
        let date = new Date();
        Object.assign(data, {
          optionName: this.optionName,
          optionValue: this.optionValue,
          platformName: this.platformName,
          createdBy: this.user,
          createdAt: date,
          updatedBy: this.user,
          updatedAt: date,
        });
        axios({
          method: "post",
          url:
            "https://services.metricsamsi.com/v1.0/dealers/Options?apiKey=81c14de2-6891-461b-9ea6-3ed218675b8f",
          headers: {
            "Content-Type": "application/json",
          },
          data: data,
        })
          .then((res) => {
            let googleId = res.data.data.googleId;
            this.$swal({
              icon: "success",
              title: "Success!",
              text: "Option successfully added!",
            }).then(() => {
              this.$emit("fetchOptions", googleId);
              this.$emit("closeModal");
            });
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
    closeModal: function ($event) {
      if (
        $event.target.className == "modal-container" ||
        $event.target.className == "close-btn"
      ) {
        this.$emit("closeModal");
      }
    },
  },
};
</script>

<style lang="scss">
.modal-container {
  position: absolute;
  background: #80808087;
  width: 100vw;
  height: 100vh;
  display: flex;
  top: 0;
  left: 0;
  .modal {
    background: white;
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
    align-items: center;
    width: 60%;
    margin: 0 auto;
    min-height: 600px;
    align-self: center;
    position: relative;
    z-index: 1;
    justify-content: space-around;
    .close-btn {
      font-size: 25px;
      font-weight: bold;
      position: absolute;
      right: 15px;
      top: 5px;
      &:hover {
        cursor: pointer;
      }
    }
    .form {
      width: 60%;
      .form-field {
        padding: 10px 0;
        border: unset;
        input {
          padding: 10px;
          width: 100%;
          font-size: 18px;
          border: unset;
          border-bottom: #0000004f 2px solid;
          transform: all 0.5s;
          &:hover {
            transform: scale(1.05);
            transition: all 0.4s;
            box-shadow: 0px 2px 8px 0px #0000001f;
          }
          &:focus-visible {
            transform: scale(1.05);
            transition: all 0.4s;
            box-shadow: 0px 2px 8px 0px #0000001f;
            outline: transparent;
          }
        }
      }
    }
  }
}
</style>

