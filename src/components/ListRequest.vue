<template>
  <div>
    <b-container>
        <router-link to="/">
            <h2><b-link href="/">Back</b-link></h2>
        </router-link>
        <h1 v-show="empty">No Items :3</h1>
        <b-card class="listRequest" v-for="(req, index) in requests" :key="index" :header="req.requester_node_id">
            <p>Request ID: {{ req.request_id }}</p>
            <p>Message: {{ req.request_message }}</p>
            <b-button variant="primary" @click="acceptRequest(index)">Accept</b-button>
            <b-button variant="danger" @click="rejectRequest(index)">Reject</b-button>
        </b-card>
    </b-container>
  </div>
</template>

<script>
export default {
  name: 'ListRequest',
  data() {
    return {
      requests: [],
      empty: false,
      namespace: '',
      identifier: '',
    };
  },
  methods: {
    acceptRequest(index) {
      const req = this.requests[index];
      this.$http.post('api/idp/accept', {
        Namespace: this.namespace,
        Identifier: this.identifier,
        RequestId: req.request_id,
      }).then(() => {
        this.requests.splice(index, 1);
      }).catch((err) => {
        alert(err.data.ErrorDesc);
        if (err.data.ErrorDesc === 'remove') this.requests.splice(index, 1);
      });
    },
    rejectRequest(index) {
      const req = this.requests[index];
      this.$http.post('api/idp/reject', {
        Namespace: this.namespace,
        Identifier: this.identifier,
        RequestId: req.request_id,
      }).then(() => {
        this.requests.splice(index, 1);
      }).catch((err) => {
        alert(err.data.ErrorDesc);
        if (err.data.ErrorDesc === 'remove') this.requests.splice(index, 1);
      });
    },
  },
  mounted() {
    this.namespace = this.$route.params.namespace;
    this.identifier = this.$route.params.identifier;
    const url = `api/idp/requests/${this.namespace}/${this.identifier}`;
    this.$http.get(url).then((res) => {
      this.requests = res.data;
      if (this.requests.length > 0) this.empty = false;
      else this.empty = true;
      for (let i = 0; i < this.requests.length; i++) {
        this.requests[i].requester_node_id = `Request From ${this.requests[i].requester_node_id}`;
      }
    }).catch((err) => {
      alert(err.data.ErrorDesc);
    });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.listRequest {
    margin-bottom: 20px;
}
</style>
