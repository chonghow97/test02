<template>
  <section id="menu">
    <!-- is-login -->
    <v-list-item two-line>
      <v-list-item-avatar>
        <img
          src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTBxpj2OOGlOnaXrRmZQ0-s3nOhCBwJEFfwZQ&usqp=CAU"
        />
      </v-list-item-avatar>

      <v-list-item-content>
        <v-list-item-title>Admin</v-list-item-title>
      </v-list-item-content>
    </v-list-item>

    <v-divider></v-divider>

    <MenuList :items="items"></MenuList>
  </section>
</template>



<script>
import MenuList from "@/components/MenuList.vue";
const data = {
  dialog: false,
  Default_Profile: "mdi-account-circle",
};
export default {
  components: { MenuList },
  data: () => data,
  props: {
    items: { title: String, icon: String, link: String },
    // admin: String,
  },
  methods: {
    Login: function (a) {
      this.login = a;
      this.$emit("login", a);
    },
  },
};
</script>

<style></style>
