<template>
  <div class="main">
    <div class="title">
      <img src="../assets/up-icon.jpg" />
      <h2>OPTIONS UPDATER</h2>
    </div>
    <div class="head">
      <select name="" id="" @change="handleFetch">
        <option value="Google View Name" disabled selected>
          Google View Name
        </option>
        <option
          v-for="(viewName, i) in this.googleViewNames"
          :value="viewName.googleId"
          :key="i"
        >
          {{ viewName.viewName }}
        </option>
      </select>
      <button
        :class="this.selectedOption ? 'active' : 'disabled'"
        @click="
          this.selectedOption ? (createModal = true) : (createModal = false)
        "
      >
        NEW
      </button>
      <CreateOption
        v-if="createModal"
        :info="viewName"
        @closeModal="closeModal"
        @fetchOptions="fetchOptions"
      />
      <button :class="this.selectedOption ? 'active' : 'disabled'">SAVE</button>
      <button :class="this.selectedOption ? 'active' : 'disabled'">
        DELETE
      </button>
    </div>
    <div class="table">
      <h4>OPTIONS</h4>
      <table>
        <tr id="table-head">
          <th>OPTION PLATFORM</th>
          <th>OPTION NAME</th>
          <th>OPTION VALUE</th>
          <th>DOT DIGITAL ID</th>
          <th></th>
        </tr>
        <tr v-if="!selectedOption"></tr>
        <tr
          v-else
          v-for="(platform, i) in selectedOption"
          :key="i"
          id="table-row"
        >
          <th>{{ platform.platformName }}</th>
          <th>{{ platform.optionName }}</th>
          <th>{{ platform.optionValue }}</th>
          <th>{{ platform.dotDigitalId }}</th>
          <th>
            <button
              class="active"
              :value="platform.rooftopGoogleOptionId"
              @click="deleteOption"
            >
              DELETE
            </button>
          </th>
          <div
            id="deleted-row"
            v-if="this.deletedRow == platform.rooftopGoogleOptionId"
          >
            <button>RESTORE</button>
          </div>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import CreateOption from "./CreateOption.vue";
export default {
  name: "Home",
  components: {
    CreateOption,
  },
  props: {},
  data() {
    return {
      googleViewNames: null,
      selectedOption: null,
      createModal: false,
      viewName: null,
      deletedRow: null,
      apiKey: "81c14de2-6891-461b-9ea6-3ed218675b8f",
    };
  },
  async created() {
    await this.fetchGoogleViewNames(this.apiKey);
  },
  methods: {
    fetchGoogleViewNames: async function (apiKey) {
      let googleViewNames = await axios
        .get(
          "https://services.metricsamsi.com/v1.0/dealers/Accounts/Google?apiKey=" +
            apiKey
        )
        .then((res) => {
          let resArr = res.data.data.filter((item) => {
            return item.viewName;
          });
          return resArr;
        });
      this.googleViewNames = googleViewNames;
    },
    handleFetch: function ($event) {
      let googleId = $event.target.value;
      this.fetchOptions(googleId);
    },
    fetchOptions: async function (googleId) {
      this.deletedRow = null;
      let apiKey = "81c14de2-6891-461b-9ea6-3ed218675b8f";
      let allOptions = await axios
        .get(
          "https://services.metricsamsi.com/v1.0/dealers/Options/" +
            googleId +
            "?apiKey=" +
            apiKey
        )
        .then((res) => {
          let resArr = res.data.data;
          return resArr;
        });
      this.selectedOption = allOptions;
      this.viewName = allOptions[0];
    },
    deleteOption: async function ($event) {
      let rooftopGoogleOptionId = $event.target.value;
      await this.$swal
        .fire({
          title: "Are you sure you want to delete this option?",
          showDenyButton: true,
          confirmButtonText: `Delete`,
          denyButtonText: `Cancel`,
        })
        .then((result) => {
          if (result.isConfirmed) {
            axios
              .delete(
                `https://services.metricsamsi.com/v1.0/dealers/Options/${rooftopGoogleOptionId}?apiKey=${this.apiKey}`,
                {
                  headers: {
                    "Content-Type": "application/json",
                  },
                }
              )
              .then((res) => {
                this.$swal.fire("Deleted!", "", "success");
              });
          } else if (result.isDenied) {
            // this.$swal.fire("Changes are not saved", "", "info");
          }
        });
      this.deletedRow = rooftopGoogleOptionId;
      // this.fetchOptions(this.viewName.googleId);
    },
    closeModal: function () {
      this.createModal = false;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
.main {
  display: flex;
  justify-content: center;
  flex-direction: column;
  max-width: 800px;
  margin: 0 auto;
  .title {
    justify-content: flex-start;
    display: flex;
    align-items: center;
    margin-bottom: 20px;
  }
  .head {
    display: flex;
    justify-content: space-around;
    border: 1px solid rgba(0, 0, 0, 0.103);
    border-radius: 6px;
    padding: 20px;
    margin-bottom: 30px;
    select {
      width: 40%;
    }
    button {
      &.disabled {
        padding: 8px 30px;
        color: #eaeaea;
        background: #cccccc;
        border: transparent;
        border-radius: 6px;
        &:hover {
          cursor: no-drop;
        }
      }
      &.active {
        padding: 8px 30px;
        color: white;
        background: #3da7de;
        border: transparent;
        border-radius: 6px;
        &:hover {
          cursor: pointer;
        }
      }
    }
  }
  .table {
    border: 1px solid rgba(0, 0, 0, 0.103);
    padding: 20px;
    border-radius: 6px;

    table {
      width: 100%;
      border-collapse: collapse;
      position: relative;
      #table-head {
        background: #f4f4f4;
        padding: 10px 0;
        height: 50px;
        th {
          font-weight: normal;
        }
      }
      #table-row {
        height: 45px;
        color: #807f7fd6;
        text-transform: uppercase;
        button {
          &.disabled {
            padding: 5px 13px;
            color: #eaeaea;
            background: #cccccc;
            border: transparent;
            border-radius: 6px;
            &:hover {
              cursor: no-drop;
            }
          }
          &.active {
            padding: 5px 13px;
            color: white;
            background: #3da7de;
            border: transparent;
            border-radius: 6px;
            &:hover {
              cursor: pointer;
            }
          }
        }
        #deleted-row {
          left: 0;
          position: absolute;
          width: 100%;
          height: inherit;
          background: #808080c2;
          display: flex;
          justify-content: center;
          align-items: center;
        }
      }
    }
    h4 {
      margin-top: unset;
      font-weight: bold;
      border-top: 1px solid rgba(0, 0, 0, 0.103);
      border-bottom: 1px solid rgba(0, 0, 0, 0.103);
      padding: 10px 0;
    }
  }
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#deleted-row {
  button {
    padding: 5px 13px;
    color: white;
    background: #3da7de;
    border: transparent;
    border-radius: 6px;
    &:hover {
      cursor: pointer;
    }
  }
}
</style>
