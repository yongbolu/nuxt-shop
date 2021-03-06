<template>
  <div>
    <section class="section no-top-pad">
      <h5 class="title is-5">管理者設置</h5>
      <hr>

      <div class="columns">
        <div class="column is-one-third">
          <form @submit.prevent="createAdmin">
            <div class="field">
              <label class="label">名字</label>
              <div class="control">
                <input class="input" type="text"
                  name="name" v-model="name"
                  v-validate="'required|min:4'"
                  :class="{ 'is-danger': errors.has('name') }">
                <p v-show="errors.has('name')"
                  class="help is-danger">{{
                  errors.first('name') }}</p>
              </div>
            </div>
            <div class="field">
              <label class="label">Email</label>
              <div class="control">
                <input class="input" type="email"
                  name="email" v-model="email"
                  v-validate="'required|email'"
                  :class="{ 'is-danger': errors.has('email') }">
                <p v-show="errors.has('email')"
                  class="help is-danger">{{
                  errors.first('email') }}</p>
              </div>
            </div>
            <div class="field">
              <label class="label">密碼</label>
              <div class="control">
                <input class="input" type="password"
                  name="password" v-model="password"
                  v-validate="'required|min:6'"
                  :class="{ 'is-danger': errors.has('password') }">
                <p v-show="errors.has('password')"
                  class="help is-danger">{{
                  errors.first('password') }}</p>
              </div>
            </div>

            <error-bar :error="error"></error-bar>

            <div class="field">
              <div class="control">
                <button type="submit" class="button is-primary"
                  :class="{ 'is-loading': busy }"
                  :disabled="busy">新增</button>
              </div>
            </div>
          </form>
        </div>
        <div class="column">
          <table class="table is-striped is-fullwidth">
            <thead>
              <tr>
                <th>#</th>
                <th>管理者列表</th>
                <th>&nbsp;</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(admin, index) in admins"
                :key="admin.id">
                <th>{{ ++index }}</th>
                <td>{{ admin.name }}</td>
                <td><a href="#" @click.prevent="removeAdmin(admin)"><span
                      class="icon has-text-danger"><i
                        class="fa fa-lg fa-times-circle"></i></span></a></td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import ErrorBar from "@/components/ErrorBar";
import apiJobMixin from "@/mixins/apiJobMixin";

export default {
  data() {
    return {
      email: "",
      password: "",
      name: ""
    };
  },
  middleware: "verify-admin",
  mixins: [apiJobMixin],
  components: {
    ErrorBar: ErrorBar
  },
  mounted: function() {
    const loadedAdmins = this.$store.getters["admin/admins"];
    if (loadedAdmins.length === 0) {
      this.$store.dispatch("admin/getAdmins");
    }
  },
  methods: {
    createAdmin() {
      this.$validator.validateAll().then(result => {
        if (result) {
          const signUpData = {
            email: this.email,
            password: this.password,
            name: this.name
          };
          this.$store.dispatch("admin/createAdmin", signUpData);
        }
      });
    },
    removeAdmin(admin) {
      this.$swal({
        title: "Delete the administrator?",
        icon: "warning",
        buttons: true,
        dangerMode: true
      }).then(ok => {
        if (ok) {
          this.$store.dispatch("admin/removeAdmin", admin);
        }
      });
    },
    jobsDone() {
      this.name = "";
      this.email = "";
      this.password = "";
      this.$nextTick(() => {
        this.removeErrors();
      });
    }
  },
  computed: {
    admins() {
      return this.$store.getters["admin/admins"];
    }
  }
};
</script>