<template>
  <div>
    {{ screenMessage }}
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "UserIdentification",

  data() {
    return {
      oAuthAccessURL: "https://slack.com/api/oauth.v2.access",
      slackAppScope: "chat:write:bot",
      screenMessage: "",
    };
  },

  computed: {
    slackAuthHref() {
      return `https://slack.com/oauth/v2/authorize?scope=${this.slackAppScope}&redirect_uri=${this.redirect_uri}&client_id=${this.client_id}`;
    },
  },

  mounted() {
    this.saveUserWebHook();
  },

  methods: {
    async saveUserWebHook() {
      const params = {
        code: this.$route.query.code,
        client_id: process.env.VUE_APP_CLIENT_ID,
        client_secret: process.env.VUE_APP_CLIENT_SECRET,
        redirect_uri: process.env.VUE_APP_REDIRECT_URI,
      };
      axios.get("https://slack.com/api/oauth.v2.access", { params }).then(
        (response) => {
          if (!response.incoming_webhook?.url) this.slackErrorHandler();

          this.screenMessage = `Sucesso! Sua URL para webHook é ${response.incoming_webhook.url}`;
        },
        (error) => {
          this.slackErrorHandler();
          console.error(error);
        }
      );
    },
    slackErrorHandler() {
      this.screenMessage =
        "Ocorreu um erro ao tentar se conectar ao Slack. Não foi possível instalar o bot.";
    },
  },
};
</script>
