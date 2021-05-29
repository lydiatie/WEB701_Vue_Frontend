<template>
  <div v-if="!submitted">
    <div class="list row ">
      <div class="col-md-4">
        <h4>Token List</h4>
        <table class="table table-hover table-bordered">
          <thead>
            <tr>
              <th>Address</th>
              <th>Quantity</th>
            </tr>
          </thead>
          <tbody>
            <tr :class="{ active: index == currentIndex }"
                v-for="(token, index) in tokens"
                :key="index"
                @click="setActiveToken(token, index);">
              <td>{{ token.address }}</td>
              <td>{{ token.quantity }}</td>
            </tr>
          </tbody>  
        </table> 

        <button class="m-3 btn btn-sm btn-danger" @click="deleteToken">
        Get Token
        </button>
      </div>
      <div class="col-md-8">
        <div v-if="currentToken">
          <div class="token"> 
            <label><strong>Quantity:</strong></label> {{ currentToken.quantity }}
            <br>
            <label><strong>Address:</strong></label> {{ currentToken.address }}
          </div>
        </div>
        <div v-else>
          <br />
          <p>Please select a Token...</p>
        </div>
      </div>
    </div>  
  </div>
<div v-else>
  <h4>Get token successful. Confirmation email will be sent to you!</h4>
  <button class="btn btn-success" @click="refreshList">Back</button>
    
</div>
  
</template>

<script>
import TokenData from "../services/TokenData.service";

export default {
  name: "tokens-list",
  data() {
    return {
      tokens: [],
      currentToken: null,
      currentIndex: -1,
      quantity: "",
      address:"",
      submitted: false
    };
  },
  methods: {
    retrieveTokens() {
      TokenData.getAll()
        .then(response => {
          this.tokens = response.data;
          console.log(response.data);
        })
        .catch(e => {
          console.log(e);
        });
    },

    refreshList() {
      this.retrieveTokens();
      this.currentToken = null;
      this.currentIndex = -1;
      this.submitted = false;
    },

    setActiveToken(token, index) {
      this.currentToken = token;
      this.currentIndex = index;
    },

    deleteToken() {
      TokenData.delete(this.currentToken._id)
        .then(response => {
          console.log(response.data);
          this.$router.push({ name: "gettokens" });
          this.submitted = true;
        })
        .catch(e => {
          console.log(e);
        });
    },
  },
  mounted() {
    this.retrieveTokens();
  }
};
</script>

<style>
.list {
  text-align: left;
  max-width: 100%;
  padding-top: 50px;
  padding-left: 250px;
}

.token {
  padding-top: 50px;
}
</style>