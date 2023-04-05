<template>
  <div v-if="contact" class="page">
    <h4>Thêm Liên hệ</h4>
    <ContactForm
      :contact="contact"
      @delete:contact="addContact"
    />
    <p>{{ message }}</p>
  </div>
</template>
<script>
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";
export default {
  components: {
    ContactForm,
  },
  props: {
    id: { type: String, required: true },
  },
  data() {
    return {
      contact: null,
      message: "",
    };
  },
  methods: {
    async getContact(id) {
      try {
        this.contact = await ContactService.get(id);
      } catch (error) {
        console.log(error);
        // Chuyển sang trang NotFound đồng thời giữ cho URL không đổi
        this.$router.push({
          name: "notfound",
          params: {
            pathMatch: this.$route.path.split("/").slice(1),
          },
          query: this.$route.query,
          hash: this.$route.hash,
        });
      }
    },
    async addContact(data) {
      try {
        await ContactService.update(this.contact._id, data);
        this.message = "Liên hệ được cập nhật thành công.";
      } catch (error) {
        console.log(error);
      }
    },
  },
  created() {
    this.getContact(this.id);
    this.message = "";
  },
};
</script>
