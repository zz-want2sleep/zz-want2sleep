<template>
  <div class="container">
    <h1 v-for="item in desc" :key="item">{{ item }}</h1>
    <a-divider />
    <div id="divType" class="typer"></div>
    <a-divider />
    <!-- grid布局 -->
    <select id="selecter">
      <option value="auto">auto</option>
      <option value="min-content">min-content</option>
      <option value="max-content">max-content</option>
      <option value="fit-content(7em)">fit-content(7em)</option>
    </select>
    <a-divider />
    <div class="dense-packing">
      <a-switch :checked="checkValue" @change="onChange" />
    </div>
    <a-divider />
    <div class="writing-mode">
      <a-select default-value="horizontal-tb" @change="handleChange">
        <a-select-option value="horizontal-tb">
          horizontal-tb
        </a-select-option>
        <a-select-option value="vertical-rl">
          vertical-rl
        </a-select-option>
        <a-select-option value="vertical-lr">
          vertical-lr
        </a-select-option>
      </a-select>
    </div>
    <a-divider />
    <div class="direction">
      <label class="toggle" for="direction">
        <span class="toggle__label">Set direction to right-to-left</span>
        <a-switch :checked="direction" id="direction" @change="onDirectionChange" />
      </label>
    </div>
    <div class="grid">
      <a-button type="primary" @click="onJump('/home')">home</a-button>
      <div class="item">Item tow</div>
      <div class="item col-div">Item has more text</div>
      <a-button type="primary" @click="onJump('/3d-case1')">3D</a-button>
      <div class="item">Item four</div>
      <a-button type="primary" @click="onJump('/css')">CSS</a-button>
      <div class="item">Item five</div>
    </div>
  </div>
  <span class="text">foo</span>
  <div class="grid-container">
    <div class="item item-1">1</div>
    <div class="item item-2">2</div>
    <div class="item item-3">3</div>
    <div class="item item-4">4</div>
    <div class="item item-5">5</div>
    <div class="item item-6">6</div>
    <div class="item item-7">7</div>
    <div class="item item-8">8</div>
    <div class="item item-9">9</div>
  </div>
  <span class="text">bar</span>
</template>

<script setup>
  import { onMounted, nextTick } from 'vue';
  import { useRouter } from 'vue-router';
  const router = useRouter();
  const onJump = (path) => {
    router.push(path)
  };

  const desc = [
    'hello guy！\n',
    '你好 这是一个打字动画效果！\n',
    '练习一下grid布局\n'
  ];
  // 打字动画 方式一 （等宽字体有中文展示缺陷）
  const animationKeyDown1 = () => {
    // 元素集合
    const elList = document.getElementsByTagName('h1');
    const txtList = []; // 等宽字体长度数量集合 一个中文等于2ch，字母数字1ch
    const arr = Array.from(elList);
    arr.forEach((item, index) => {
      let len = item.innerText.length, s = item.style;
      let chLength = 0;
      item.innerText.split('').forEach(txt => {
        /[\u4e00-\u9fa5]/.test(txt) ? chLength += 2 : chLength += 1;
      })
      s.width = `${chLength}ch`;
      txtList.push(chLength);
      if (index !== 0) {
        const len = txtList.slice(0, index).reduce((total, num) => total + num);
        s.animationDelay = `${len/6}s`;
      }
      s.animationTimingFunction = `steps(${len}), steps(2)`;
      s.animationIterationCount = (index !== (arr.length-1)) ? `1, ${Math.ceil(chLength/6)}` : `1, infinite`
      s.animationDuration = `${chLength/6}s, 1s`;
    });
  };
  // 打字动画 方式二 (把多行文字分割后分别放在一个span里面，然后利用css3的animation逐个实现文字颜色的动画显示。span文字颜色和背景设置为一样，然后顺序改变每个字的颜色)
  const textList = [];
  const animationKeyDown2 = () => {
    desc.join('').split('').forEach((char) => {
      if(char==="\n") {
        textList.push("<span style=' flex-basis: 100%; height: 0;'></span>") ;
      } else {
        textList.push("<span class='box'>" + char.replace(/\s/g, "&nbsp;") + "</span>")
      }
    });
    document.getElementById('divType').innerHTML = textList.join('');
    const textDelayList = [];
    const spans = document.querySelectorAll('.typer span');
    const spanArr = Array.from(spans);

    spanArr.forEach(async (item, index) => {
      let s = item.style;
      item.classList.add('caret2');
      item.classList.add('changetowhite');
      await nextTick();
      textDelayList.push(0.3);
      if (index !== 0) {
        const txtDelay = textDelayList.slice(0, index).reduce((total, num) => total + num);
        s.animationDelay = `${txtDelay}s, ${txtDelay}s`;
      }
      s.animationTimingFunction = 'steps(0), steps(2)';
      s.animationIterationCount = index === (spanArr.length - 2) ? '1, infinite' : '1, 1';
      s.animationDuration = '0.3s, 0.3s';
    });
  };

  // grid布局事件
  const gridEvent = () => {
    const root = document.documentElement;
    const selecter = document.querySelector('#selecter');

    selecter.addEventListener('change', (evt) => {
      root.style.setProperty('--var-col', evt.target.value);
    });
  };

  const checkValue = $ref(false);
  const onChange = (checked) => {
    checkValue = checked;
    const root = document.documentElement;
    root.style.setProperty('--var-packing', checked ? 'row dense' : 'row');
  };

  const handleChange = (val) => {
    const root = document.documentElement;
    root.style.setProperty('--var-writing-mode', val);
  };

  const direction = $ref(false);
  const onDirectionChange = (checked) => {
    direction = checked;
    const root = document.documentElement;
    root.style.setProperty('--var-direction', checked ? 'rtl' : 'ltr');
  };
  
  onMounted(() => {
    animationKeyDown1();
    animationKeyDown2();
    gridEvent();
  });
</script>

<style lang="less">
:root {
  --var-col: auto;
  --var-packing: row;
  --var-direction: rtl;
  --var-writing-mode: horizontal-tb;
}
/* 方式一：等宽字体+动画实现 打字 */
/* 
解决两个问题
1.这个动画是连贯的，如何逐字出现？
（用steps()进行处理）
2.我们目前指定了一个宽度 180px， 这个是写死的，不同长度的文本，我们应该怎么去计算？
（用ch这个css单位去缓解,
ch 它表示的是 “0” 字形的宽度，
而在等宽字体中，
“0” 字形的宽度和其他所有字形的宽度是一样的
，因此问题可以用 ch 单位来表达文字的宽度）
*/
@keyframes typing {
  from {
    width: 0;
    visibility: visible;
  }
  to {
    visibility: visible;
  }
}
@keyframes caret {
  from {
    border-color: transparent;
  }
}

  .container {
    // width: 100vw;
    text-align: center;
    // 打字动画 方式一
    h1 {
      visibility: hidden;
      margin: 0 auto;
      font: bold 20px monospace; /* 等宽字体 */
      /* width: 10ch; */
      overflow: hidden; /* 隐藏超出部分 */
      white-space: nowrap; /* 文本不换行 */
      border-right: 2px solid #000; /* 模拟光标 */
      
      /* 使用js来计算字符数量并赋值steps函数 */
      animation: typing forwards,
              caret reverse forwards; /* 包括空格和符号，一共10个字符*/
    }
    .grid {
      display: grid;
      grid-template-columns: repeat(3, var(--var-col));
      // grid-template-columns: repeat(3, 1fr);
      // grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
      grid-auto-rows: minmax(100px, auto);
      grid-auto-flow: var(--var-packing);
      direction: var(--var-direction);
      writing-mode: var(--var-writing-mode);
      gap: 10px;
      padding: 1em;
      // max-width: 30rem;
      // width: 100vw;
      background-color: aliceblue;
      .item {
        background-color: aquamarine;
        grid: auto / span 1;
      }
      .col-div {
        grid-column: auto / span 2;
        grid-row: auto / span 2;
      }
    }
  }
  @keyframes changecolor {
    from {
      color: #000;
    }
    to {
      color: #fff;
    }
  }
  @keyframes caret2 {
    from {
      border-color: #000;
    }
    to {
      border-color: #fff;
    }
  }
  // 打字动画 方式二
  .typer{
    display: flex;
    flex-wrap: wrap;
    background-color: #000;
    padding: 10px;
    font: bold 20px monospace; /* 等宽字体 */
    .box {
      display: inline-block;
      color: #000;
      &.changetowhite{
        // animation: changecolor forwards;
        &.caret2 {
          border-right: 2px solid #000;
          animation: changecolor forwards,
                      caret2;
        }
      }
    }
  }
  .grid-container {
    padding: 10px;
    display: grid;
    grid-template-columns: repeat(3, 100px);
    grid-template-rows: repeat(3, 100px);
    gap: 10px 10px;
    grid-auto-flow: row dense;
    grid-auto-rows: 50px;
    grid-auto-columns: 50px;
    place-items: stretch stretch;
    place-content: stretch stretch;
    .item {
      font-size: 2em;
      text-align: center;
      border: 1px solid #181038;
    }

    .item-1 {
      background-color: #ef342a;
      grid-column-start: 1;
      grid-column-end: 3;
      grid-row-start: 2;
      grid-row-end: 4;
      // z-index: 1;
    }

    .item-2 {
      background-color: #f68f26;
      grid-column-start: 2;
      grid-column-end: 3;
      grid-row-start: 1;
      grid-row-end: 3;
      text-align: center;
      display: flex;
      width: 100%;
      align-items: center;
      justify-content: center;
    }

    .item-3 {
      background-color: #4ba946;
    }

    .item-4 {
      background-color: #0376c2;
    }

    .item-5 {
      background-color: #c077af;
    }

    .item-6 {
      background-color: #f8d29d;
    }
  }
</style>

