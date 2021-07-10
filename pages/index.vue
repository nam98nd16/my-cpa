<template>
  <div>
    <a-row>
      <a-col :md="12" :xs="24"
        ><hotTable
          :data="trueDataSource"
          :rowHeaders="false"
          :colHeaders="[
            'Semester',
            'Course ID',
            'Course Title',
            'Credit',
            'Grade',
          ]"
          :columns="[
            { data: 'semester', readOnly: true },
            { data: 'courseId', readOnly: true },
            { data: 'courseTitle', readOnly: true },
            { data: 'courseCredit', readOnly: true },
            { data: 'courseGrade', validator: gradeValidator },
          ]"
          :undo="true"
          :columnSorting="true"
          licenseKey="non-commercial-and-evaluation"
        ></hotTable
      ></a-col>
      <a-col :md="6" :xs="24">
        <div>
          <a-button
            type="primary"
            @click="shouldHide0CreditCourses = !shouldHide0CreditCourses"
            >{{
              shouldHide0CreditCourses
                ? "Unhide 0-credit courses"
                : "Hide 0-credit courses"
            }}</a-button
          >
        </div>
        <div style="margin-top: 5px">
          <a-input-number :min="2" :max="10" v-model="numOfDecimalDigits" />
          decimal digits
        </div>
      </a-col>
      <a-col :md="6" :xs="24">
        <div>
          CPA: {{ cpa.toFixed(numOfDecimalDigits) }} over
          {{ totalCredits }} credits
        </div>
      </a-col>
    </a-row>
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
      totalCredits: 0,
      shouldHide0CreditCourses: true,
      numOfDecimalDigits: 2,
    };
  },
  components: {
    HotTable,
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
      this.trueDataSource.forEach((course) => {
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
    },
    trueDataSource() {
      return !this.shouldHide0CreditCourses
        ? this.dataSource
        : this.dataSource.filter((course) => course.courseCredit);
    },
  },
  mounted() {
    let i = 0;
    for (let course in this.allCourses) {
      let trueCourse = {
        semester: this.allCourses[course][0],
        courseId: this.allCourses[course][1],
        courseTitle: this.allCourses[course][2],
        courseCredit: this.allCourses[course][3],
        courseGrade: this.allCourses[course][4],
      };
      this.$set(this.dataSource, i, trueCourse);
      i++;
    }
  },
  methods: {
    gradeValidator(value, callback) {
      if (
        [
          "A",
          "A+",
          "a",
          "a+",
          "B",
          "b",
          "B+",
          "b+",
          "C",
          "c",
          "C+",
          "c+",
          "D",
          "D+",
          "d",
          "d+",
          "F",
          "f",
        ].includes(value)
      )
        callback(true);
      else callback(false);
    },
  },
};
</script>

<style src="../node_modules/handsontable/dist/handsontable.full.css"></style>