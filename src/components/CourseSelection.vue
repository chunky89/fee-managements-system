<template>
  <div class="course-selection">
    <h2>Select a Course</h2>
    <select v-model="selectedCourse" @change="emitSelection">
      <option disabled value="">Please select one</option>
      <option v-for="course in courses" :key="course.id" :value="course.id">
        {{ course.name }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: "CourseSelection",
  props: {
    courses: {
      type: Array,
      required: true,
    }
  },
  data() {
    return {
      selectedCourse: ""
    };
  },
  methods: {
    emitSelection() {
      this.$emit('course-selected', this.selectedCourse);
    }
  }
};
</script>

<style scoped>
.course-selection {
  display: flex;
  flex-direction: column;
  max-width: 300px;
}
select {
  margin-top: 8px;
  padding: 4px;
}
</style>