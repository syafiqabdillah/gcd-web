<template>
  <div class=" text-left">
    <div align="center">
      <h3>
        Contact Us
      </h3>
    </div>

    <div class="contact-us-container">
      <b-row>
        <b-col></b-col>
        <b-col cols="12" md="6">
          <b-card>
            <b-form>
              <b-form-group
                id="input-contact-us-email-group"
                label="Email"
                label-for="input-contact-us-email"
              >
                <b-form-input
                  id="input-contact-us-email"
                  v-model="form.email"
                  type="email"
                  required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                id="input-contact-us-subject-group"
                label="Subject"
                label-for="input-contact-us-subject"
              >
                <b-form-input
                  id="input-contact-us-subject"
                  v-model="form.subject"
                  required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                id="input-contact-us-message-group"
                label="Message"
                label-for="input-contact-us-message"
              >
                <b-form-textarea
                  id="input-contact-us-message"
                  v-model="form.message"
                  rows="6"
                ></b-form-textarea>
              </b-form-group>

              <b-button block type="submit" variant="primary">
                <b-spinner
                  type="grow"
                  small
                  v-if="isAddingLocation"
                ></b-spinner>
                {{ isAddingLocation ? "Submitting" : "Submit" }}
              </b-button>
            </b-form>
          </b-card>
        </b-col>
        <b-col></b-col>
      </b-row>
    </div>
  </div>
</template>

<script>
export default {
  name: "ContactUs",
  data() {
    return {
      isSendingMessage: false,
      form: {
        email: "",
        subject: "",
        message: "",
      },
    };
  },
};
</script>

<style scoped>
.contact-us-container {
  margin: 30px;
}
button {
    background-color: #318fb5;
}
</style>
