### **心理学实验代码教程**

#### 一、**实验名称** 

视觉线索切换任务

#### 二、实验目的

本实验旨在研究参与者如何根据视觉提示对颜色和形状进行分类，并测量他们的反应时间。

#### 三、实验流程

1. **全屏模式**：确保实验在全屏模式下进行，以减少干扰。
2. **实验说明**：展示实验介绍图片，让参与者了解实验目的和流程。
3. **注视点呈现**：显示“+”字注视点，提示参与者集中注意力。
4. **参照图案呈现**：展示参照图案，让参与者了解判断任务。
5. **判断任务**：随机呈现多个图案，参与者需根据颜色或形状进行按键反应。
6. **空屏**：每个判断任务之间有短暂黑屏，为下一次反应做准备。
7. **实验结束**：显示结束语，感谢参与者。

#### 四、代码解析

##### **1.引入jsPsych库和插件**

```html
<!-- js插件 按需引入 -->
<script src="jspsych/jspsych.js"></script>
<script src="jspsych/plugin-html-keyboard-response.js"></script>
<script src="jspsych/plugin-image-keyboard-response.js"></script>
<script src="jspsych/plugin-html-button-response.js"></script>
<script src="jspsych/plugin-html-keyboard-response.js"></script>
<script src="jspsych/plugin-fullscreen.js"></script>
<script src="jspsych/plugin-instructions.js"></script>
```

这些脚本文件是jsPsych的核心和一些必要的插件，用于实现实验的各种功能。

##### 2.设置背景等样式

```css
body {
    background-color: black;
    color: white;
}

.jspsych-display-element {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.text-prompt {
    /* 调整图片与文字的间距 */
    margin-bottom: 20px;
    text-align: center;
    font-size: 64px;
}

.stimulus-image {
    /* 根据需要调整图片大小 */
    max-width: 80%;
    max-height: 80%;
}
```

这些样式定义了页面的背景颜色、文字颜色以及实验内容的显示方式。

##### 3.图片预加载

```javascript
// 图片加载
var image = new Array()
function preload() {
    for (i = 0; i < preload.arguments.length; i++) {
        image[i] = new Image()
        image[i].src = preload.arguments[i]
    }
}
preload(
    "img/greencir.png", "img/greentri.png", "img/introduction.jpg", "img/redcir.png", "img/redtri.png"
)
```

预加载所有实验中使用的图片，以确保实验过程中图片能够快速显示。

##### 4.初始化jsPsych

```javascript
/* initialize jsPsych */
const jsPsych = initJsPsych({
    // 实验结束时的回调函数
    on_finish: function() {
        var csvData = jsPsych.data.get().csv();
        var blob = new Blob([csvData], { type: 'text/csv;charset=utf-8;' });
        var link = document.createElement('a');
        var url = URL.createObjectURL(blob);
        link.setAttribute('href', url);
        link.setAttribute('download', 'data.csv');
        link.style.visibility = 'hidden';
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }
})
```

初始化jsPsych实例，并设置在实验结束时下载数据的回调函数。

##### 5.定义试次数据与参数

```javascript
// 定义试次数据
var trial_list = []

// 定义试次参数
var trials = [
    {stimulus: "img/redtri.png", text_prompt: "C"},
    {stimulus: "img/redcir.png", text_prompt: "C"},
    {stimulus: "img/greentri.png", text_prompt: "C"},
    {stimulus: "img/greencir.png", text_prompt: "C"},
    {stimulus: "img/redtri.png", text_prompt: "S"},
    {stimulus: "img/redcir.png", text_prompt: "S"},
    {stimulus: "img/greentri.png", text_prompt: "S"},
    {stimulus: "img/greencir.png", text_prompt: "S"}
]
```

定义每个试次的参数，如刺激图片和提示文本。

##### 6.重复试次和随机化试次顺序

```javascript
// 定义重复次数
var num_repetitions = 4; 
trials = trials.flatMap(function(trial) {
    let trials_array = [];
    for (let i = 0; i < num_repetitions; i++) {
        trials_array.push({
            stimulus: trial.stimulus,
            text_prompt: trial.text_prompt
        })
    }
    return trials_array
})
// 重复试次和随机化试次顺序
trials = jsPsych.randomization.shuffle(trials)

// 将试次参数转换为试次
trials.forEach(function(trial) {
    let trial_object = {
        type: jsPsychHtmlKeyboardResponse,
        stimulus: '<div class="text-prompt">' + trial.text_prompt + '</div>' +
        '<img class="stimulus-image" src="' + trial.stimulus + '">',
        // 定义响应键 F/J
        choices: ['F', 'J'], 
        on_finish: function(data) {
            console.log('Trial data:', data);
        }    
    };
    // 每次试验前黑屏
    trial_list.push(blank);
    trial_list.push(trial_object);
})
```

将每个试次重复指定次数，并随机化试次顺序。

##### 7.**定义实验流程**

```javascript
// 初始化加载项
var timeline = []

// 将节点添加到timeline数组中
timeline.push(enter_fullscreen)
timeline.push(instruction)
timeline.push(fixation)
timeline.push(blank)
timeline.push(reference)
timeline.push(blank)

// 将trial_list数组中的每个元素添加到timeline数组中
trial_list.forEach(function(trial) {
    timeline.push(trial)
    console.log(trial)
})

timeline.push(end)
```

定义实验的流程，包括全屏提示、说明、注视点、参照图案、试次和结束语。

##### 8.**运行实验**

```javascript
jsPsych.run(timeline)
```

开始运行实验，按照定义的流程执行。



#### 五、**结束语**

 感谢您阅读本教程。希望这能帮助您理解实验的代码和流程。如果您有任何疑问或需要进一步的帮助，请随时联系我们。



**联系方式：**

- **姓名：** 陈颖
- **邮箱：** chenyingcci@163.com