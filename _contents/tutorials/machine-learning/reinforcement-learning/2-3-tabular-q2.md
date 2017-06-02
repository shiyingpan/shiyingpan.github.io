---
youku_id: XMTk3MTU4Njg2MA
youtube_id: JafbuFHZPdc
chapter: 2
title: Q-learning 思维决策
thumbnail: /static/thumbnail/rl/5 qlearning 2.jpg
publish-date: 2017-01-09
---

* 学习资料:
  * [全部代码](https://github.com/MorvanZhou/Reinforcement-learning-with-tensorflow/tree/master/contents/2_Q_Learning_maze)
  * [什么是 Q Learning 短视频]({% link _contents/tutorials/machine-learning/ML-intro/4-03-q-learning.md %})
  * 本节内容的模拟视频效果[Youtube](https://www.youtube.com/watch?v=G5BDgzxfLvA), [优酷](http://v.youku.com/v_show/id_XMTg3NTI2Mzg3Ng==.html)
  * 学习书籍 [Reinforcement learning: An introduction](http://ufal.mff.cuni.cz/~straka/courses/npfl114/2016/sutton-bookdraft2016sep.pdf)

接着上节内容, 我们来实现 `RL_brain` 的 `QLearningTable` 部分, 这也是 RL 的大脑部分, 负责决策和思考.


#### 本节内容包括:

* [代码主结构](#main-structure)
* [预设值](#setting)
* [决定行为](#action)
* [学习](#learn)
* [检测 state 是否存在](#check)

<h4 class="tut-h4-pad" id="main-structure">代码主结构</h4>

与上回不一样的地方是, 我们将要以一个 class 形式定义 Q learning, 并把这种 tabular q learning 方法叫做 `QLearningTable`.

```python
class QLearningTable:
    # 初始化
    def __init__(self, actions, learning_rate=0.01, reward_decay=0.9, e_greedy=0.9):

    # 选行为
    def choose_action(self, observation):

    # 学习更新参数
    def learn(self, s, a, r, s_):

    # 检测 state 是否存在
    def check_state_exist(self, state):
```


<h4 class="tut-h4-pad" id="setting">预设值</h4>

初始的参数意义不会在这里提及了, 请参考这个快速了解通道 [机器学习系列-Q learning](#)

```python
import numpy as np
import pandas as pd


class QLearningTable:
    def __init__(self, actions, learning_rate=0.01, reward_decay=0.9, e_greedy=0.9):
        self.actions = actions  # a list
        self.lr = learning_rate # 学习率
        self.gamma = reward_decay   # 奖励衰减
        self.epsilon = e_greedy     # 贪婪度
        self.q_table = pd.DataFrame(columns=self.actions)   # 初始 q_table
```

<h4 class="tut-h4-pad" id="action">决定行为</h4>

这里是定义如何根据所在的 state, 或者是在这个 state 上的 观测值 (observation) 来决策.

```python
    def choose_action(self, observation):
        self.check_state_exist(observation) # 检测本 state 是否在 q_table 中存在(见后面标题内容)

        # 选择 action
        if np.random.uniform() < self.epsilon:  # 选择 Q value 最高的 action
            state_action = self.q_table.ix[observation, :]

            # 同一个 state, 可能会有多个相同的 Q action value, 所以我们乱序一下
            state_action = state_action.reindex(np.random.permutation(state_action.index))
            action = state_action.argmax()

        else:   # 随机选择 action
            action = np.random.choice(self.actions)

        return action
```

<h4 class="tut-h4-pad" id="learn">学习</h4>

同[上一个简单的 q learning 例子]({% link _contents/tutorials/machine-learning/reinforcement-learning/2-1-general-rl.md %})一样,
我们根据是否是 `terminal` state (回合终止符) 来判断应该如何更行 `q_table`. 更新的方式是不是很熟悉呢:

`update = self.lr * (q_target - q_predict)`

这可以理解成神经网络中的更新方式, 学习率 * (真实值 - 预测值). 将判断误差传递回去, 有着和神经网络更新的异曲同工之处.

```python
    def learn(self, s, a, r, s_):
        self.check_state_exist(s_)  # 检测 q_table 中是否存在 s_ (见后面标题内容)
        q_predict = self.q_table.ix[s, a]
        if s_ != 'terminal':
            q_target = r + self.gamma * self.q_table.ix[s_, :].max()  # 下个 state 不是 终止符
        else:
            q_target = r  # 下个 state 是终止符
        self.q_table.ix[s, a] += self.lr * (q_target - q_predict)  # 更新对应的 state-action 值
```

<h4 class="tut-h4-pad" id="check">检测 state 是否存在</h4>

这个功能就是检测 `q_table` 中有没有当前 state 的步骤了, 如果还没有当前 state, 那我我们就插入一组全 0 数据, 当做这个 state 的所有 action 初始 values.

```python
    def check_state_exist(self, state):
        if state not in self.q_table.index:
            # append new state to q table
            self.q_table = self.q_table.append(
                pd.Series(
                    [0]*len(self.actions),
                    index=self.q_table.columns,
                    name=state,
                )
            )
```

如果想一次性看到全部代码, 请去我的 [Github](https://github.com/MorvanZhou/Reinforcement-learning-with-tensorflow/tree/master/contents/2_Q_Learning_maze)
