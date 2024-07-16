<template>
  <div class="flex flex-col justify-center items-center h-screen space-y-5">
    <div class="text-5xl font-black">Hello SignUpInOut Project!</div>
    <div class="flex flex-row gap-2">
      <UButton to="/signup">Sign Up</UButton>
      <UButton to="/signin">Sign In</UButton>
    </div>
  </div>
</template>

<script setup lang="ts">
import * as signalR from "@microsoft/signalr";

onMounted(() => {
  const connection = new signalR.HubConnectionBuilder()
    .withUrl("http://localhost:12345/hub")
    .build();

  connection.on("messageReceived", (user, message) => {
    console.log(user + ' says: "' + message + '"');
    alert(user + ' says: "' + message + '"');
  });

  connection
    .start()
    .then(() => {
      console.log("SignalR Connected");
      // connection.send("newMessage", "test", "Hello world!");
    })
    .catch((err) => console.error("SignalR Connection Error: ", err));
});
</script>
