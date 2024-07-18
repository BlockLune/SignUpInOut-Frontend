<template>
  <div class="min-w-80 space-y-10">
    <div class="flex flex-row justify-center">
      <div class="text-5xl font-bold">Sign In</div>
    </div>
    <div class="flex flex-col space-y-4">
      <UButton
        color="purple"
        class="justify-center"
        to="https://discord.com/oauth2/authorize?client_id=1261643473153560636&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%3A3000%2Fsignin&scope=identify+email"
        >Sign in with Discord</UButton
      >
      <UForm
        :schema="schema"
        :state="state"
        class="space-y-2"
        @submit="onSubmit"
      >
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
  </div>
</template>

<script setup lang="ts">
import { z } from "zod";
import type { FormSubmitEvent } from "#ui/types";
import axiosInstance from "~/utils/axiosInstance";

const schema = z.object({
  email: z.string().email("Invalid email").max(255, "Email is too long"),
  password: z
    .string()
    .min(8, "Must be at least 8 characters")
    .max(255, "Password is too long"),
});

type Schema = z.output<typeof schema>;

const state = reactive({
  email: undefined,
  password: undefined,
});

async function onSubmit(event: FormSubmitEvent<Schema>) {
  console.log(event.data);
  axiosInstance
    .post("/api/users/signin", {
      email: event.data.email,
      password: event.data.password,
    })
    .then((response) => {
      console.log(response.data);
      if (response.status === 200) {
        alert("Sign in successful!");
      } else {
        alert("Sign in failed!");
      }
    })
    .catch((error) => {
      console.error(error);
    });
}

const route = useRoute();

onMounted(async () => {
  const code = route.query.code as string;
  if (code) {
    try {
      const response = await axiosInstance.post(
        `/api/discord/signin?code=${code}`
      );
      console.log(response.data);
      if (response.status === 200) {
        alert("Sign in successful!");
      } else {
        alert("Sign in failed!");
      }
    } catch (err) {
      console.error(err);
    }
  }
});
</script>
