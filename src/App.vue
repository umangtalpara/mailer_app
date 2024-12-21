<template>
  <v-container class="d-flex justify-center align-center fill-height" fluid>
    <v-card
      class="pa-4"
      elevation="4"
      :style="{ height: '100%', maxWidth: cardWidth }"
    >
      <v-card-title class="justify-center text-h5 font-weight-bold">
        Email Send
      </v-card-title>
      <v-card-text class="d-flex flex-column">
        <v-form @submit.prevent="submitForm" class="flex-grow-1" ref="emailForm">
          <v-text-field
            v-model="formData.toEmail"
            label="To Email"
            outlined
            dense
            clearable
            required
          ></v-text-field>
          <v-text-field
            v-model="formData.subject"
            label="Subject"
            outlined
            dense
            clearable
          ></v-text-field>
          <v-textarea
            v-model="formData.message"
            label="Message"
            outlined
            dense
            rows="4"
          ></v-textarea>
          <v-card-actions class="justify-center mt-4">
            <v-btn
              :disabled="!isFormValid"
              color="primary"
              variant="elevated"
              class="gradient-btn"
              type="submit"
            >
              Send
            </v-btn>
          </v-card-actions>
        </v-form>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script>
import { ref, reactive, computed, onMounted } from "vue";

export default {
  name: "EmailForm",
  setup() {
    const isLaptop = ref(false);
    const isTablet = ref(false);

    const formData = reactive({
      toEmail: "",
      subject: "",
      message: "",
    });

    const cardWidth = computed(() => {
      if (isLaptop.value) return "600px";
      if (isTablet.value) return "500px";
      return "400px";
    });

    const isFormValid = computed(() => {
      return formData.toEmail.trim() && formData.subject.trim() && formData.message.trim();
    });

    const resetForm = () => {
      formData.toEmail = "";
      formData.subject = "";
      formData.message = "";
    };

    const updateSize = () => {
      const width = window.innerWidth;
      isLaptop.value = width >= 1024;
      isTablet.value = width >= 768 && width < 1024;
    };

    const submitForm = async () => {
      if (!isFormValid.value) {
        alert("All fields are required.");
        return;
      }

      try {
        const response = await fetch("http://localhost:3000/mailer/send", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(formData),
        });

        if (!response.ok) {
          throw new Error("Failed to send email.");
        }

        const result = await response.json();
        console.log("Email sent successfully:", result);
        alert("Email sent successfully!");

        // Clear the form
        resetForm();
      } catch (error) {
        console.error("Error sending email:", error);
        alert("Failed to send email.");
      }
    };

    onMounted(() => {
      updateSize();
      window.addEventListener("resize", updateSize);
    });

    return { isLaptop, isTablet, cardWidth, formData, isFormValid, submitForm, resetForm };
  },
};
</script>

<style>
.gradient-btn {
  background: linear-gradient(to right, #4facfe, #9796f0);
  color: white;
}

.gradient-btn:hover {
  background: linear-gradient(to left, #4facfe, #9796f0);
}

body {
  background: linear-gradient(to bottom, #6a11cb, #2575fc);
  margin: 0;
  height: 100vh;
}

.v-application--wrap {
  height: 100%;
}
</style>
