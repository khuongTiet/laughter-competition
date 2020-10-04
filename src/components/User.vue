<script>
import axios from "axios";

export default {
  data() {
    return {
      imgUrl: "",
    };
  },
  props: {
    name: String,
    hp: Number,
    dps: Number,
    enemyDPS: Number,
    time: Number,
    randomize: Function,
  },
  computed: {
    battleHP: function () {
      return this.hp - this.enemyDPS * this.time;
    },
  },
  methods: {
    getImage: function () {
      axios
        .get("https://api.thecatapi.com/v1/images/search", {
          headers: {
            "x-api-key": "03a90364-9687-4ca5-970f-0022b3cb413c",
          },
        })
        .then((response) => {
          const [cat] = response.data;
          console.log("response: ", cat);
          this.imgUrl = cat.url;
        });
    },
  },
  mounted() {
    this.getImage();
  },
  render() {
    return (
      <div class="column is-one-quarter is-offset-1 centered-card">
        <div class="card">
          <div class="card-image">
            <figure class="image is-4x3">
              <img src={this.imgUrl} alt="Placeholder image" />
            </figure>
          </div>
          <div class="card-content">
            <div class="content">
              <p class="title is-4 has-text-black">{this.name}</p>
              <div>
                <span class="icon">
                  <i class="fas fa-laugh"></i>
                  <p class="sub-title is-4 has-text-black">{` ${this.dps}/sec`}</p>
                </span>
              </div>

              <div>
                <label for="hp">{`HP: ${this.battleHP} / ${this.hp}`}</label>
                <progress
                  id="hp"
                  class="progress is-success"
                  value={this.battleHP}
                  max={this.hp}
                ></progress>
              </div>
            </div>
          </div>
        </div>

        <div>
          <a
            class="button is-primary"
            onClick={() => {
              this.randomize();
              this.getImage();
              this.$forceUpdate();
              this.battleHP = this.hp;
            }}
          >
            Randomize
          </a>
        </div>
      </div>
    );
  },
};
</script>
<style scoped>
.centered-card {
  display: flex;
  flex-direction: column;
  align-self: center;
}
</style>

