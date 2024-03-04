<template>
  <div class="points-list">
    <p v-if="points.length > 0" class="section-title">Selected Points:</p>
    <ul>
      <li v-for="(point, index) in points" :key="index">
        ({{ point.x }}, {{ point.y }})
      </li>
    </ul>
    <p v-if="points.length === 3" class="section-title">Area of Parallelogram: {{ parallelogramArea }}</p>
    <p v-if="points.length === 3" class="section-title">Area of Circle: {{ circleArea }}</p>
    <p v-if="points.length === 3" class="section-title">Center of Circle: ({{ circleCenter.x }}, {{ circleCenter.y }})</p>
    <button @click="reset" v-show="points.length === 3">Reset</button>
  </div>
</template>

<script>
export default {
  props: {
    points: {
      type: Array,
      default: () => []
    },
    parallelogramArea: Number,
    circleArea: Number,
    circleCenter: Object
  },
  methods: {
    reset() {
      this.$emit('reset');
    }
  }
};
</script>

<style>
.points-list {
  background-color: #f9f9f9;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.section-title {
  font-weight: bold;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  margin: 5px 0;
}

button {
  margin-top: 10px;
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #0056b3;
}
</style>