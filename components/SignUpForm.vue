<template>
  <div class="min-w-80 space-y-10">
    <div class="flex flex-row justify-center">
      <div class="text-5xl font-bold">Sign Up</div>
    </div>
    <UForm :schema="schema" :state="state" class="space-y-2" @submit="onSubmit">
      <UFormGroup label="Email" name="email" required>
        <UInput
          v-model="state.email"
          icon="i-heroicons-envelope"
          placeholder="Please input your email"
        />
      </UFormGroup>
      <UFormGroup label="Password" name="password" required>
        <UInput
          v-model="state.password"
          type="password"
          icon="i-heroicons-key"
          placeholder="Please input your password"
        />
      </UFormGroup>
      <UFormGroup
        label="Confirm Your Password"
        name="passwordForConfirm"
        required
      >
        <UInput
          v-model="state.passwordForConfirm"
          type="password"
          icon="i-heroicons-key"
          placeholder="Type your password again to confirm"
        />
      </UFormGroup>
      <UFormGroup label="Captcha" name="captcha" required>
        <div class="flex flex-row justify-center" @click="fetchCaptcha">
          <img :src="captchaSrc" alt="captcha" />
        </div>
        <UInput v-model="state.captcha" placeholder="Captcha" />
      </UFormGroup>
      <div class="flex flex-row justify-between gap-2 pt-5">
        <div class="grow">
          <UButton block color="gray" to="/"> Go Back </UButton>
        </div>
        <div class="grow">
          <UButton type="submit" block> Submit </UButton>
        </div>
      </div>
    </UForm>
  </div>
</template>

<script setup lang="ts">
import { z } from "zod";
import type { FormSubmitEvent } from "#ui/types";
import axiosInstance from "~/utils/axiosInstance";

const schema = z.object({
  email: z.string().max(255, "Email is too long").email("Invalid email"),
  password: z
    .string()
    .min(8, "Must be at least 8 characters")
    .max(255, "Password is too long"),
  passwordForConfirm: z.string().refine((data) => data === state.password, {
    message: "Passwords do not match",
  }),
  captcha: z.string(),
});

type Schema = z.output<typeof schema>;

const state = reactive({
  email: undefined,
  password: undefined,
  passwordForConfirm: undefined,
  captcha: undefined,
});

const captchaId = ref<number | undefined>(undefined);
const captchaSrc = ref<string | undefined>(undefined);

const fetchCaptcha = async () => {
  try {
    const response = await axiosInstance.get("/api/captcha");
    captchaId.value = response.data.captchaId;

    const captchaResponse = await axiosInstance.get(
      `/api/captcha/${captchaId.value}`,
      {
        responseType: "blob",
      }
    );
    captchaSrc.value = URL.createObjectURL(captchaResponse.data);
  } catch (error) {
    console.error("Failed to fetch captcha:", error);
  }
};

onMounted(() => {
  fetchCaptcha();
});

async function onSubmit(event: FormSubmitEvent<Schema>) {
  console.log(event.data);
  axiosInstance
    .post("/api/users", {
      email: event.data.email,
      password: event.data.password,
    })
    .then((response) => {
      console.log(response);
      if (response.status === 200) {
        alert("Sign up success!");
      } else {
        alert("Sign up failed!");
      }
    })
    .catch((error) => {
      console.error(error);
    });
}
</script>
