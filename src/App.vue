<template>
  <div id="app">
    <canvas id="canvas" width="800" height="600" @mousedown="startDragging" @mousemove="dragPoint"
      @mouseup="endDragging" @click="selectPoint"></canvas>
    <PointsList :points="points" :parallelogramArea="parallelogramArea" :circleArea="circleArea"
      :circleCenter="circleCenter" @reset="reset" />
    <AboutTemplate />
  </div>
</template>

<script>
import AboutTemplate from './components/AboutTemplate.vue';
import PointsList from './components/PointsList.vue';

export default {
  data() {
    return {
      points: [],
      parallelogramArea: 0,
      circleArea: 0,
      circleCenter: { x: 0, y: 0 },
      dragging: false,
      draggedPointIndex: -1
    };
  },
  methods: {
    selectPoint(event) {
      if (this.points.length === 3 || this.dragging) return;

      const rect = event.target.getBoundingClientRect();
      const x = event.clientX - rect.left;
      const y = event.clientY - rect.top;
      this.points.push({ x, y });
      if (this.points.length === 3) {
        this.calculateShapes();
      }
    },
    startDragging(event) {
      if (this.points.length === 3) {
        const rect = event.target.getBoundingClientRect();
        const x = event.clientX - rect.left;
        const y = event.clientY - rect.top;
        const pointIndex = this.findClosestPoint(x, y);
        if (pointIndex !== -1) {
          this.dragging = true;
          this.draggedPointIndex = pointIndex;
        }
      }
    },
    dragPoint(event) {
      if (this.dragging && this.points.length === 3) {
        const rect = event.target.getBoundingClientRect();
        const x = event.clientX - rect.left;
        const y = event.clientY - rect.top;
        this.points[this.draggedPointIndex].x = x;
        this.points[this.draggedPointIndex].y = y;
        this.calculateShapes();
      }
    },
    endDragging() {
      this.dragging = false;
      this.draggedPointIndex = -1;
    },
    calculateShapes() {
      const [A, B, C] = this.points;

      const AB = { x: B.x - A.x, y: B.y - A.y };
      const AC = { x: C.x - A.x, y: C.y - A.y };
      this.parallelogramArea = Math.abs(AB.x * AC.y - AB.y * AC.x);

      const a = Math.sqrt(AB.x ** 2 + AB.y ** 2);
      const b = Math.sqrt(AC.x ** 2 + AC.y ** 2);
      const c = Math.sqrt((B.x - C.x) ** 2 + (B.y - C.y) ** 2);
      const s = (a + b + c) / 2;
      const circleRadius = (a * b * c) / (4 * Math.sqrt(s * (s - a) * (s - b) * (s - c)));
      this.circleArea = Math.PI * circleRadius ** 2;
      const centerX = (A.x + B.x + C.x) / 3;
      const centerY = (A.y + B.y + C.y) / 3;
      this.circleCenter = { x: centerX, y: centerY };

      this.drawParallelogram(A, B, C);
      this.drawCircle(centerX, centerY, circleRadius);

      this.drawSelectedPoints();
    },
    drawParallelogram(A, B, C) {
      const canvas = document.getElementById('canvas');
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.beginPath();
      ctx.moveTo(A.x, A.y);
      ctx.lineTo(B.x, B.y);
      ctx.lineTo(C.x, C.y);
      ctx.closePath();
      ctx.strokeStyle = 'blue';
      ctx.stroke();
    },
    drawCircle(centerX, centerY, radius) {
      const canvas = document.getElementById('canvas');
      const ctx = canvas.getContext('2d');
      ctx.beginPath();
      ctx.arc(centerX, centerY, radius, 0, 2 * Math.PI);
      ctx.strokeStyle = 'yellow';
      ctx.stroke();
    },
    reset() {
      this.points = [];
      this.parallelogramArea = 0;
      this.circleArea = 0;
      this.circleCenter = { x: 0, y: 0 };
      const canvas = document.getElementById('canvas');
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    },
    findClosestPoint(x, y) {
      for (let i = 0; i < this.points.length; i++) {
        const point = this.points[i];
        const distance = Math.sqrt((x - point.x) ** 2 + (y - point.y) ** 2);
        if (distance <= 5) {
          return i;
        }
      }
      return -1;
    },
    drawSelectedPoints() {
      const canvas = document.getElementById('canvas');
      const ctx = canvas.getContext('2d');
      ctx.fillStyle = 'red';
      this.points.forEach(point => {
        ctx.beginPath();
        ctx.arc(point.x, point.y, 5.5, 0, Math.PI * 2);
        ctx.fill();
      });
    }
  },
  components: {
    AboutTemplate,
    PointsList
  }
};
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 20px;
  font-family: 'Arial', sans-serif;
  /* Change to your desired font */
}

#canvas {
  border: 1px solid black;
  cursor: crosshair;
}

.info {
  margin-top: 20px;
}

.about {
  margin-top: 20px;
  font-size: 16px;
  color: #555;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  margin: 5px 0;
}

button {
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
