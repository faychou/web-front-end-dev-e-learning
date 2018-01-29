# 动画
传统 web 动画大多数都通过直接操作实际 DOM 元素来实现，这在 React 中显然是不被提倡的。那么，在 React 中动画都是如何实现的呢？
所有动画的本质都是连续修改 DOM 元素的一个或者多个属性，使其产生连贯的变化效果，从而形成动画。在 React 中实现动画本质上与传统 web 动画一样，仍然是两种方式： 通过 css3 动画实现和通过 js 修改元素属性。只不过在具体实现时，要更为符合 React 的框架特性，可以概括为几类：

* 基于定时器或 requestAnimationFrame(RAF) 的间隔动画；
* 基于 css3 的简单动画；
* React 动画插件 CssTransitionGroup；
* 结合 hook 实现复杂动画；
* 其他第三方动画库。

## 一、基于定时器或 RAF 的间隔动画
动画的实现都是依靠定时器 setInterval，setTimeout 或者 requestAnimationFrame(RAF) 直接修改 DOM 元素的属性。不熟悉 React 特性的开发者可能会习惯性地通过 ref 或者 findDOMNode() 获取真实的 DOM 节点，直接修改其样式。然而，通过 ref 直接获取真实 DOM 并对其操作是是不被提倡使用，应当尽量避免这种操作。

而正确的做法是将定时器或者 RAF 等方法与 DOM 节点属性通过 state 联系起来。首先，需要提取出与变化样式相关的属性，替换为 state，然后在合适的生命周期函数中添加定时器或者 requestAnimationFrame 不断修改 state，触发组件更新，从而实现动画效果。

``` js
// 使用 requestAnimationFrame 改变 state，从而实现的进度条动画
import React, { Component } from 'react';

export default class Progress extends Component {
    constructor(props) {
        super(props);
        this.state = {
            percent: 10
        };
    }

    increase = () => {
        const percent = this.state.percent;
        const targetPercent = percent >= 90 ? 100 : percent + 10;
        const speed = (targetPercent - percent) / 400;
        let start = null;
        const animate = timestamp => {
            if (!start) start = timestamp;
            const progress = timestamp - start;
            const currentProgress = Math.min(parseInt(speed * progress + percent, 10), targetPercent);
            this.setState({
                percent: currentProgress
            });
            if (currentProgress < targetPercent) {
                window.requestAnimationFrame(animate);
            }
        };
        window.requestAnimationFrame(animate);
    }

    decrease = () => {
        const percent = this.state.percent;
        const targetPercent = percent < 10 ? 0 : percent - 10;
        const speed = (percent - targetPercent) / 400;
        let start = null;
        const animate = timestamp => {
            if (!start) start = timestamp;
            const progress = timestamp - start;
            const currentProgress = Math.max(parseInt(percent - speed * progress, 10), targetPercent);
            this.setState({
                    percent: currentProgress
                });
            if (currentProgress > targetPercent) {
                window.requestAnimationFrame(animate);
            }
        };
        window.requestAnimationFrame(animate);
    }

    render() {
        const { percent } = this.state;

        return (
            <div>
                <div className="progress">
                    <div className="progress-wrapper" >
                        <div className="progress-inner" style = {{width: `${percent}%`}} ></div>
                    </div>
                    <div className="progress-info" >{percent}%</div>
                </div>
                <div className="btns">
                    <button onClick={this.decrease}>-</button>
                    <button onClick={this.increase}>+</button>
                </div>
            </div>
        );
    }
}
```

## 二、基于 css3 的简单动画
``` js
//使用 css3 实现进度条
import React, { Component } from 'react';

export default class Progress extends Component {
    constructor(props) {
        super(props);
        this.state = {
            percent: 10
        };
    }

    increase = () => {
        const percent = this.state.percent + 10;
        this.setState({
            percent: percent > 100 ? 100 : percent,
        })
    }

    decrease = () => {
        const percent = this.state.percent - 10;
        this.setState({
            percent: percent < 0 ? 0 : percent,
        })
    }

    render() {
        // 同上例， 省略
        //....
    }
}
.progress-inner {
  transition: width 400ms cubic-bezier(0.08, 0.82, 0.17, 1);
  // 其他样式同上，省略
  ...
}
```

## 三、React 动画插件
React 曾为开发者提供过动画插件 react-addons-css-transition-group，后交由社区维护，形成现在的 react-transition-group，该插件可以方便地实现组件的入场和离场动画，使用时需要开发者额外安装。react-transition-group 包含 CSSTransitionGroup 和 TransitionGroup 两个动画插件，其中，后者是底层 api，前者是后者的进一步封装，可以较为便捷地实现 css 动画。

## 四、结合 hook 实现复杂动画
在实际项目中，可能需要一些更炫酷的动画效果，这些效果仅依赖于 css3 往往较难实现。此时，我们不妨借助一些成熟的第三方库，如 jQuery 或 GASP，结合 React 组件中的生命周期钩子方法 hook 函数，实现复杂动画效果。在这些 hook 函数中结合第三方动画库可以实现丰富的入场、离场动画效果。

## 五、其他第三方动画库
此外，还有很多优秀的第三方动画库，如 react-motion，Animated，velocity-react等。

