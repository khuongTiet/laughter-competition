<script>
import axios from "axios";
import User from "./User.vue";

export default {
  data() {
    return {
      users: [],
      battleHP: [],
      time: 0,
    };
  },
  methods: {
    contest: function () {
      let battle = setInterval(() => {
        let user1HP = this.battleHP[0];
        let user2HP = this.battleHP[1];

        user1HP -= this.users[1].dps;
        user2HP -= this.users[0].dps;

        this.battleHP[0] = user1HP;
        this.battleHP[1] = user2HP;

        this.time += 1;

        if (user1HP <= 0 || user2HP <= 0) {
          clearInterval(battle);

          if (user1HP <= 0) {
            this.winner = 1;
          } else if (user2HP <= 0) {
            this.winner = 0;
          }
        }
      }, 1000);
    },
  },
  mounted() {
    axios.get("https://jsonplaceholder.typicode.com/users").then((response) => {
      if (response.data) {
        const users = response.data;
        const random1 = Math.floor(Math.random() * users.length);
        let random2 = Math.floor(Math.random() * users.length);

        while (random2 === random1) {
          random2 = Math.floor(Math.random() * users.length);
        }

        const user1 = {
          name: users[random1].name,
          hp: Math.floor(Math.random() * users[random1].name.length * 10),
          dps: Math.floor(Math.random() * users[random1].name.length * 5),
        };

        const user2 = {
          name: users[random2].name,
          hp: Math.floor(Math.random() * users[random2].name.length * 10),
          dps: Math.floor(Math.random() * users[random2].name.length * 5),
        };

        this.users = [user1, user2];
        this.battleHP = [user1.hp, user2.hp];
      }
    });
  },
  render() {
    return (
      <div class="columns max-height">
        <User
          name={this.users[0].name}
          hp={this.users[0].hp}
          dps={this.users[0].dps}
          enemyDPS={this.users[1].dps}
          time={this.time}
        />
        <a
          class="column is-one-fifth is-offset-1  button is-primary centered-button"
          onClick={this.contest}
        >
          Contest
        </a>
        <User
          name={this.users[1].name}
          hp={this.users[1].hp}
          dps={this.users[1].dps}
          enemyDPS={this.users[0].dps}
          time={this.time}
        />
        <div class="column is-one-quarter is-offset-1" />
      </div>
    );
  },
};
</script>
<style scoped>
.max-height {
  height: 100vh;
}

.centered-button {
  display: flex;
  flex-direction: column;
  align-self: center;
}
</style>
