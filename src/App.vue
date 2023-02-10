<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor
      ref="resumeEditor"
      :markdown="currentMarkdown"
      :enableHtml="enableHtml"
    ></ResumeEditor>
  </div>
</template>

<script>
import StyleEditor from "./components/StyleEditor";
import ResumeEditor from "./components/ResumeEditor";
import "./assets/reset.css";

export default {
  name: "app",
  components: {
    StyleEditor,
    ResumeEditor,
  },
  data() {
    return {
      interval: 10,
      currentStyle: "",
      enableHtml: false,
      fullStyle: [
        `
const name = 'Nova Cheng';
const githubAccount = 'https://github.com/chenghuiyuan';
const stackoverflowAccount = 'https://stackoverflow.com/users/11929921/cheng-hui-yuan';


function workExperience() {
    return {
        'Dec 2021 to Current' : 
        {'Senior Software Engineer @ A5Tec (Fei Siong Group)': 
        ['Tech lead for Android-based POS system (flutter, kotlin, sqlite): 3 engineers', 
        'Tech lead for Tap-A-Meal project (react native, express js, mysql): 2 engineers',
        "Key responsibilities: managing development timeline, code review, software architecture, deployment"]},

        'Sep 2020 to Dec 2021' : 
        {'Software Engineer @ A5Tec (Fei Siong Group)': 
        ['Key member in Agile team to develop internal web-based POS system (python, postgresql, mongoDB)',
        'Key contributor in Agile team to develop e-commerce platform for bulk purchase (react, mysql)']},

        'April 2020 to Sep 2020':
        {'Programmer @ Zetta Solutions': 
        ['Developed food-ordering system from scratch for restaurant (php, vanilla js, css, mysql)',
        'Developed new features for ERP systems for multiple clients (php, Vanilla js, css, mysql)']},

        'Dec 2019 to April 2020':
        {'Software Engineer Intern @ Thales': 
        ['Refactored digital identity mobile app (react native, hyperledger blockchain)',
        'Developed mobile app POC (flutter, hyperledger blockchain)']},

        'July 2019 to Dec 2019': 
        {'Web Development Intern @ Philips Electronics': 
        ['Developed new features for task management system (laravel, vue js, jquery, css, mysql, apache)',
        'Collaborated with external vendor to connect IoT device to mysql database (python)']}
    };
}

function education() {
    return {
        '2018 to 2022': 
        {'Bachelor of Technology (Hons) in Software Engineering @ National University of Singapore': 
        {"Dean's List": ['2019/2020 Sem 2']}},  
        '2014 - 2017': 
        {'Diploma in Food Science and Nutrition @ Nanyang Polytechnic':
        {"Director's List": ['Year 1 Sem 1','Year 1 Sem 2', 'Year 2 Sem 1', 'Year 2 Sem 2', 'Year 3 Sem 2']}
        }
    };
}


`,
      ],
    };
  },
  created() {
    this.makeResume();
  },

  methods: {
    makeResume: async function () {
      await this.progressivelyShowStyle(0);
      await this.progressivelyShowStyle(1);
      await this.progressivelyShowStyle(2);

      await this.progressivelyShowResume();
      await this.showHtml();
    },
    showHtml: function () {
      return new Promise((resolve, reject) => {
        this.enableHtml = true;
        resolve();
      });
    },
    progressivelyShowStyle(n) {
      return new Promise((resolve, reject) => {
        let interval = this.interval;
        let showStyle = async function () {
          let style = this.fullStyle[n];
          if (!style) {
            return;
          }
          // 计算前 n 个 style 的字符总数
          let length = this.fullStyle
            .filter((_, index) => index <= n)
            .map((item) => item.length)
            .reduce((p, c) => p + c, 0);
          let prefixLength = length - style.length;
          if (this.currentStyle.length < length) {
            let l = this.currentStyle.length - prefixLength;
            let char = style.substring(l, l + 1) || " ";
            this.currentStyle += char;
            if (style.substring(l - 1, l) === "\n") {
              this.$nextTick(() => {
                this.$refs.styleEditor.goBottom();
              });
            }
            setTimeout(showStyle, interval);
          } else {
            resolve();
          }
        }.bind(this);
        showStyle();
      });
    },
    progressivelyShowResume() {
      return new Promise((resolve, reject) => {
        let length = this.fullMarkdown.length;
        let interval = this.interval;
        let showResume = () => {
          if (this.currentMarkdown.length < length) {
            this.currentMarkdown = this.fullMarkdown.substring(
              0,
              this.currentMarkdown.length + 1
            );
            let lastChar =
              this.currentMarkdown[this.currentMarkdown.length - 1];
            let prevChar =
              this.currentMarkdown[this.currentMarkdown.length - 2];
            if (prevChar === "\n" && this.$refs.resumeEditor) {
              this.$nextTick(() => this.$refs.resumeEditor.goBottom());
            }
            setTimeout(showResume, interval);
          } else {
            resolve();
          }
        };
        showResume();
      });
    },
  },
};
</script>

<style scoped>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

html {
  min-height: 100vh;
}
* {
  box-sizing: border-box;
}
</style>
