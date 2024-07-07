<template>
  <div class="min-w-80">
    <div class="flex flex-row justify-center">
      <div class="text-xl font-bold">Sign Up</div>
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
      <UButton type="submit"> Submit </UButton>
    </UForm>
  </div>
</template>

<script setup lang="ts">
import { z } from "zod";
import type { FormSubmitEvent } from "#ui/types";

const schema = z.object({
  email: z.string().email("Invalid email"),
  password: z.string().min(8, "Must be at least 8 characters"),
  passwordForConfirm: z.string().refine((data) => data === state.password, {
    message: "Passwords do not match",
  }),
});

type Schema = z.output<typeof schema>;

const state = reactive({
  email: undefined,
  password: undefined,
  passwordForConfirm: undefined,
});

async function onSubmit(event: FormSubmitEvent<Schema>) {
  console.log(event.data);
}
</script>
