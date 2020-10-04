<script>
import axios from "axios";
import User from "./User.vue";
import Celebration from "./Celebration.vue";

export default {
  data() {
    return {
      userPool: [],
      users: [],
      battleHP: [],
      time: 0,
      winner: null,
    };
  },
  methods: {
    contest: function () {
      let battle = setInterval(() => {
        this.battleHP[0] -= this.users[1].dps;
        this.battleHP[1] -= this.users[0].dps;

        this.time += 1;

        if (this.battleHP[0] <= 0 || this.battleHP[1] <= 0) {
          clearInterval(battle);

          if (this.battleHP[0] <= 0 && this.battleHP[1] <= 0) {
            this.winner = null;
          } else if (this.battleHP[0] <= 0) {
            this.winner = 1;
          } else if (this.battleHP[1] <= 0) {
            this.winner = 0;
          }
        }
      }, 1000);
    },
    getRandomUser: function (seed) {
      return {
        name: this.userPool[seed].name,
        hp: Math.floor(Math.random() * this.userPool[seed].name.length * 10),
        dps: Math.floor(Math.random() * this.userPool[seed].name.length * 5),
      };
    },
    getRandomSeed: function () {
      return Math.floor(Math.random() * this.userPool.length);
    },
    randomizeUser: function (user) {
      const random = this.getRandomSeed();
      this.battleHP = [this.users[0].hp, this.users[1].hp];
      this.users[user] = this.getRandomUser(random);
      this.winner = null;
      this.time = 0;
      this.$forceUpdate();
    },
  },
  mounted() {
    axios.get("https://jsonplaceholder.typicode.com/users").then((response) => {
      if (response.data) {
        this.userPool = response.data;
        const random1 = this.getRandomSeed();
        let random2 = this.getRandomSeed();

        while (random2 === random1) {
          random2 = this.getRandomSeed();
        }

        const user1 = this.getRandomUser(random1);

        const user2 = this.getRandomUser(random2);

        this.users = [user1, user2];
        this.battleHP = [user1.hp, user2.hp];
      }
    });
  },
  render() {
    return (
      <div class="centered-button">
        {this.winner !== null && <Celebration />}

        <div class="columns max-height">
          <User
            name={this.users[0].name}
            hp={this.users[0].hp}
            dps={this.users[0].dps}
            enemyDPS={this.users[1].dps}
            time={this.time}
            randomize={() => {
              console.log("calling randomize");
              this.randomizeUser(0);
            }}
          />

          <div class="column is-one-fifth is-offset-1 custom-center">
            {this.winner !== null && (
              <h1 class="title">
                The winner is: {this.users[this.winner].name}
              </h1>
            )}
            {this.battleHP[0] <= 0 && this.battleHP[1] <= 0 ? (
              <h1 class="title">TIED</h1>
            ) : null}
            <a class="  button is-primary" onClick={this.contest}>
              Contest
            </a>
          </div>

          <User
            name={this.users[1].name}
            hp={this.users[1].hp}
            dps={this.users[1].dps}
            enemyDPS={this.users[0].dps}
            time={this.time}
            randomize={() => this.randomizeUser(1)}
          />
          <div class="column is-one-quarter is-offset-1" />
        </div>
      </div>
    );
  },
};
</script>
<style scoped>
.max-height {
  height: 100vh;
}

.custom-center {
  display: flex;
  flex-direction: column;
  align-self: center;
}
</style>
