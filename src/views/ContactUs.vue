<template>
  <div class="container text-left">
    <div align="center" class="mb-4">
      <h3>
        Contact Us
      </h3>
    </div>

    <div class="contact-us-container d-flex flex-row justify-content-center">
        <div style="width: 65%">
          <b-card style="padding: 25px 50px 0px 50px;" class="mb-4">
            <b-form>
              <b-form-group
                id="input-contact-us-email-group"
                label="Email*"
                label-for="input-contact-us-email"
              >
                <b-form-input
                  id="input-contact-us-email"
                  v-model="form.email"
                  type="email"
                  placeholder="Enter Your Email Address"
                  required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                id="input-contact-us-subject-group"
                label="Subject*"
                label-for="input-contact-us-subject"
              >
                <b-form-input
                  id="input-contact-us-subject"
                  v-model="form.subject"
                  placeholder="Input Your Subject"
                  required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                id="input-contact-us-message-group"
                label="Message*"
                label-for="input-contact-us-message"
              >
                <b-form-textarea
                  id="input-contact-us-message"
                  v-model="form.message"
                  placeholder="Input Your Message"
                  required
                  rows="6"
                ></b-form-textarea>
              </b-form-group>

              <div class="d-flex flex-row justify-content-center">
                <b-button block type="submit" style="width: 50%; background-color: #4F59B0; border-width: 0px; padding: 10px;">
                <b-spinner
                  type="grow"
                  small
                  v-if="isAddingLocation"
                ></b-spinner>
                {{ isAddingLocation ? "Sending" : "Send" }}
              </b-button>

              </div>
            </b-form>
          </b-card>
        </div>
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
  width: 100%;
}
button {
    background-color: #318fb5;
}

input, textarea {
  border-width: 0;
  border-bottom-width: 2px;
}

input:focus, textarea:focus {
  border-width: 0;
  border-bottom-width: 2px;
  box-shadow: 0 1px 1px rgba(81, 203, 238, 1);
  color: black;
}

input:focus::placeholder, textarea:focus::placeholder {
  color: rgba(81, 203, 238, 1);
  
}

label {
  color: black;
}


</style>
