<template>
  <div class="container">
    <div class="row">
      <div class="lift-shaft">
        <div class="elevator" :style="elevatorStyles">
          <div class="inner-e" :style="innerElevatorStyles">
            <label>
              {{ endFloor }}
            </label>
          </div>
        </div>
      </div>
      <div class="floors">
        <div
          class="floor"
          v-for="(key, idx) in Object.keys(matchNumberToString).reverse()"
          :key="idx"
        >
          <button
            @click="callElevator(key)"
            type="button"
            :class="{
              selected: this.queue.filter((num) => num == key).length != 0,
            }"
            class="absolute top-0 right-0"
          >
            {{ key }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      startFloor: "first",
      endFloor: "first",
      queue: [],
      intervalId: null,
      relax: false,
      matchNumberToString: {
        first: `400%`,
        second: `300%`,
        third: `200%`,
        fourth: `100%`,
        fifth: `0%`,
      },
    };
  },
  computed: {
    innerElevatorStyles() {
      if (this.relax) {
        return {
          "animation-name": `blinker`,
          "animation-duration": `3s`,
        };
      }
      return {};
    },
    elevatorStyles() {
      return {
        "animation-name": `elevator-move-${this.startFloor}-${this.endFloor}`,
        transform: `translateY(${this.matchNumberToString[this.endFloor]})`,
      };
    },
  },
  methods: {
    runQueue() {
      if (this.queue.length == 0) {
        return;
      }
      let newNumberFloor = Object.keys(this.matchNumberToString).indexOf(
        this.queue[0]
      );
      this.intervalId = setInterval(() => {
        let indexEnd = Object.keys(this.matchNumberToString).indexOf(
          this.endFloor
        );
        console.log(indexEnd);
        if (Math.abs(newNumberFloor - indexEnd) > 1) {
          if (newNumberFloor > indexEnd) {
            indexEnd += 1;
          } else {
            indexEnd -= 1;
          }
          this.startFloor = this.endFloor;
          this.endFloor = Object.keys(this.matchNumberToString)[indexEnd];
        } else if (Math.abs(newNumberFloor - indexEnd) == 1) {
          this.startFloor = this.endFloor;
          this.endFloor = Object.keys(this.matchNumberToString)[newNumberFloor];
          this.relax = false;
          this.queue = this.queue.splice(1);
        } else {
          newNumberFloor = Object.keys(this.matchNumberToString).indexOf(
            this.queue[0]
          );
        }
      }, 1000);
    },
    callElevator(numberFloor) {
      if (
        this.queue.filter((num) => num == numberFloor).length ||
        (this.endFloor == numberFloor && this.queue.length == 0)
      ) {
        return;
      }
      this.queue = [...this.queue, numberFloor];
    },
  },
  watch: {
    async queue(newValue, oldValue) {
      console.log(newValue, oldValue);
      if (oldValue.length == 0) {
        this.runQueue();
      }
      if (newValue.length == 0) {
        clearInterval(this.intervalId);
      }
      setTimeout(() => {
        this.relax = true;
      }, 4000);
    },
  },
};
</script>

<style>
.container {
  position: relative;
}
.row {
  display: flex;
}
.floors {
  width: 80%;
}
.floor {
  border-top: 5px solid #7aa899;
  border-bottom: 5px solid #27263d;
  background: #3b3a3d;
  height: 50px;
  padding: 10px 0;
}
.lift-shaft {
  width: 20%;
}
.elevator {
  height: 60px;
  font-size: 16px;
  padding: 10px 0;
  background-color: #8b7aab;
  line-height: 18px;
  text-align: center;
  text-decoration: none;
  display: block;
  position: relative;
  animation-duration: 1s;
}
.selected {
  background-color: #8b7aab;
}
@keyframes blinker {
  0% {
    opacity: 1;
  }
  25% {
    opacity: 0.5;
  }
  50% {
    opacity: 1;
  }
  75% {
    opacity: 0.7;
  }
  100% {
    opacity: 1;
  }
}
@keyframes elevator-move-first-second {
  from {
    transform: translateY(400%);
  }
  to {
    transform: translateY(300%);
  }
}
@keyframes elevator-move-second-first {
  from {
    transform: translateY(300%);
  }
  to {
    transform: translateY(400%);
  }
}
@keyframes elevator-move-second-third {
  from {
    transform: translateY(300%);
  }
  to {
    transform: translateY(200%);
  }
}
@keyframes elevator-move-third-second {
  from {
    transform: translateY(200%);
  }
  to {
    transform: translateY(300%);
  }
}
@keyframes elevator-move-third-fourth {
  from {
    transform: translateY(200%);
  }
  to {
    transform: translateY(100%);
  }
}
@keyframes elevator-move-fourth-third {
  from {
    transform: translateY(100%);
  }
  to {
    transform: translateY(200%);
  }
}
@keyframes elevator-move-fourth-fifth {
  from {
    transform: translateY(100%);
  }
  to {
    transform: translateY(0%);
  }
}
@keyframes elevator-move-fifth-fourth {
  from {
    transform: translateY(0%);
  }
  to {
    transform: translateY(100%);
  }
}
</style>
