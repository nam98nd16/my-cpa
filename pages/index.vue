<template>
  <div style="display: flex;
  justify-content: center;">
    <hotTable
      :data="dataSource"
      :rowHeaders="false"
      :allowRemoveRow="true"
      :colHeaders="['Semester', 'Course ID', 'Course Title', 'Credit', 'Grade']"
      licenseKey="non-commercial-and-evaluation"
    ></hotTable>
    <div>CPA: {{cpa.toFixed(2)}} over {{totalCredits}} credits</div>
  </div>
</template>

<script>
import { HotTable } from "@handsontable/vue";
import { allCourses } from "../utils/const";

export default {
  data() {
    return {
      dataSource: [],
      allCourses,
      totalCredits: 0
    };
  },
  components: {
    HotTable
  },
  computed: {
    cpa() {
      let cpa = 0;
      let totalGrade = 0;
      this.totalCredits = 0;
      let handleCourseGrade = (grade, courseCredit) => {
        totalGrade += grade * courseCredit;
        this.totalCredits +=
          typeof courseCredit === "number"
            ? courseCredit
            : parseInt(courseCredit);
      };
      this.dataSource.forEach(course => {
        if (["A", "A+", "a", "a+"].includes(course.courseGrade))
          handleCourseGrade(4, course.courseCredit);
        else if (["B+", "b+"].includes(course.courseGrade))
          handleCourseGrade(3.5, course.courseCredit);
        else if (["B", "b"].includes(course.courseGrade))
          handleCourseGrade(3, course.courseCredit);
        else if (["C+", "c+"].includes(course.courseGrade))
          handleCourseGrade(2.5, course.courseCredit);
        else if (["C", "c"].includes(course.courseGrade))
          handleCourseGrade(2, course.courseCredit);
        else if (["D+", "d+"].includes(course.courseGrade))
          handleCourseGrade(1.5, course.courseCredit);
        else if (["D", "d"].includes(course.courseGrade))
          handleCourseGrade(1, course.courseCredit);
        else if (["F", "f"].includes(course.courseGrade))
          handleCourseGrade(0, course.courseCredit);
      });
      cpa = totalGrade / this.totalCredits;
      return cpa;
    }
  },
  mounted() {
    let i = 0;
    for (let course in this.allCourses) {
      let trueCourse = {
        semester: this.allCourses[course][0],
        courseId: this.allCourses[course][1],
        courseTitle: this.allCourses[course][2],
        courseCredit: this.allCourses[course][3],
        courseGrade: this.allCourses[course][4]
      };
      this.$set(this.dataSource, i, trueCourse);
      i++;
    }
  },
  methods: {}
};
</script>

<style src="../node_modules/handsontable/dist/handsontable.full.css"></style>