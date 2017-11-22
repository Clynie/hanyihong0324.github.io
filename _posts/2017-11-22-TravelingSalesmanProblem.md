---
layout: post
title: Solve Traveling Salesman Problem with GA
date:   2017-11-12 15:34:32 +0900
excerpt: Genetic Algorithm
---

# Solve Traveling Salesman Problem with GA

****

## What is TSP

有一个尺寸M*N的地图，地图上有n个城市，现在有一个推销员(Salesman)要周游(Traveling)这些城市，求一条不重复不遗漏且总路程最小的路线

Wikipedia: [旅行推销员问题](https://zh.wikipedia.org/wiki/%E6%97%85%E8%A1%8C%E6%8E%A8%E9%94%80%E5%91%98%E9%97%AE%E9%A2%98)

## Why Difficult

常规的基于数学的探索算法的复杂度可能随城市数量增加呈指数增长，即时间复杂度最坏可能为O(a^n)

举个例子，如果有三个城市，那么在给定一个起点的条件下所有可能的路线是2\*1=2种，如果是四个城市的话就是3\*2\*1=6种，如果是这种以遍历所有路线来探索最短路线的算法的话（这是最容易想到也是最傻逼的算法），其时间复杂度甚至可以到O(n!)

## GA in TSP

### Theory

*假说：若一条路线是最优路线，则其片段应也是局部最优路线*

假如有10个城市，其编号分别是0~9，且有最优路线0→1→2→3→4→5→6→7→8→9，那么任意取一个片段3→4→5应该也是周游3，4，5这三个城市的最佳路线

这个假说可以用反证法证实，如果周游3，4，5这三个城市的最佳路线不是3→4→5而是5→4→3，那么一定有distance(0→1→2→5→4→3→6→7→8→9)<distance(0→1→2→3→4→5→6→7→8→9)，0→1→2→3→4→5→6→7→8→9就不是最佳路线，我们可以根据这个假说来设计GA

再说明以下GA的逻辑：在整个世代（Generation）中选择(Select)出适应性(Fitness)高的个体，让他们进行交配(Crossover)会有大概率产生适应性更高的后代，如此反复迭代最终就可以产生一个适应性最优的个体。具体来说，一条路线就是一个个体，一条路线的周游顺序就是其基因序列(DNA)，由于每个城市座标已知，所以每一条路线的总里程可以计算，这个总里程作为这个个体的适应值(Fitness)，我们追求该值越低越好，首先随机选出适应值较低的个体，然后让其进行随机交配，具体方式是随机截取一个亲代的一段DNA复制到到子代DNA里相同的位置，剩下的位置按照另一个亲代的DNA里的顺序填充，如下图，截取了一个亲代的DNA序列[6,7,8]放到子代相同的位置，然后把剩下的数字按照另一个亲代DNA里的顺序填充进去。最后为了增加基因多样性，防止陷入局部最优化，对一部分交配产生的新个体执行变异(Mutation)操作，完全随机重排其DNA序列

**Parents:**
![](http://www.theprojectspot.com/images/post-assets/crossover_parents.jpg)

**Child:**
![](http://www.theprojectspot.com/images/post-assets/crossover_children.jpg)

以上就是GA解TSP的基本思路，其实有了上次非洲部落偷渡欧洲经验，TSP的实装并不是很难，如果代码结构好的话，是有相当一部分代码和逻辑可以重用的

**每个函数的实现后都会附上相应的测试代码以观察函数行为，便于理解我在这一步里做了什么，最后这些测试代码需要全部注释掉以执行真正的完整的GA流程**

### Initialization

```python
import random
import math
import matplotlib.pyplot as plt

# initialize
map_size_x = 100
map_size_y = 100
maps = []
city_num = 10
generation = []
population_size = 100
mutation_prob = 0.05
iter_num = 50
```
- GA模拟演算需要随机数模块random支持
- 计算欧氏距离需要math模块里的sqrt函数
- 迭代的动态可视化需要matplotlib模块
- 地图尺寸指定为100*100
- 城市数量指定为10
- GA的迭代人口数量设定为100
- GA的变异概率设定为5%
- GA的迭代次数设定为50次

### Map Initialization

```python
def map_init():
    for i in range(city_num):
        city_x = random.randint(0, map_size_x)
        city_y = random.randint(0, map_size_y)
        maps.append([city_x, city_y])
    return maps
# map init test
maps = map_init()
for i in range(city_num):
    print(maps[i])
```
maps列表记录所有城市的座标，map_init函数在合法范围内随机生成一系列城市座标添加进maps列表里实现地图的初期化，具体发生了什么可以看单元测试部分的输出

### Generation Initialization

```python
def generation_init():
    the_seed = []
    for i in range(city_num):
        the_seed.append(i)
    for i in range(population_size):
        random.shuffle(the_seed)
        generation.append(the_seed[:])
    return generation


# a debugging function to print generation in a cute format
def print_generation(generation: list):
    for i in range(population_size):
        print(i, generation[i])

# generation init test
maps = map_init()
generation = generation_init()
print_generation(generation)
```

generation列表里记录规定人口数量的路线，generation的初期化就是随机生成一堆路线填充进去，这里首先生成一个长成这样[0,1,2,3,4,5......]的缺省路线the_seed（稍微致敬一下SAO），然后用random模块里的shuffle函数不断随机重排the_seed获得不重复不遗漏的随机路线添加进generation里。同时还设计了一个打印generation的辅助函数print_generation以便观察generation的内容，具体发生了什么可以看单元测试部分的输出

### Fitness

```python
def fitness(route: list) -> float:
    def distance(city0, city1):
        x0 = maps[city0][0]
        y0 = maps[city0][1]
        x1 = maps[city1][0]
        y1 = maps[city1][1]
        dis = math.sqrt((x0 - x1) ** 2 + (y0 - y1) ** 2)
        return dis

    distance_sum = 0
    for i in range(city_num - 1):
        distance_sum += distance(route[i], route[i + 1])
    return distance_sum


# a debugging function to get average fitness of generation
def gen_fitness_avr():
    fitness_sum = 0
    for i in range(population_size):
        fitness_sum += fitness(generation[i])
    return fitness_sum / population_size


def fitness_list():
    fitlist = []
    for route in generation:
        fitlist.append(fitness(route))
    return fitlist

# fitness test
maps = map_init()
generation = generation_init()
for i in range(population_size):
    print(i, fitness(generation[i]))
```

这一步主要设计Fitness相关的函数，首先是计算一个个体的Fitness，即计算一条路径的总里程，首先设计一个计算两点间欧氏距离的函数distance，然后用一个for循环遍历路线里的每一段路程最后累加在distance_sum里返回出来。另外还设计了一个求generation全体的平均fitness的函数gen_fitness_avr用来监测整个generation的进化情况，如果进化正常这个值应该总体呈现降低的趋势。最后为了后期的方便，设计了一个计算generation里所有个体的fitness然后储存起来的函数fitness_list，可以一下获得整个generation里所有个体的fitness。单元测试fitness test会按顺序打印初期generation里每个个体的fitness

### Select

```python
def select():
    def tournament(route0: list, route1: list):
        if fitness(route0) < fitness(route1):
            return route0
        else:
            return route1

    new_generation = []
    for i in range(population_size):
        p0 = random.choice(generation)
        p1 = random.choice(generation)
        winner = tournament(p0, p1)
        new_generation.append(winner)
    return new_generation

# select test -> pass
maps = map_init()
generation = generation_init()
print(gen_fitness_avr(generation))
gen_selected = select()
print(gen_fitness_avr(gen_selected))
```

这次的select函数基本照抄上次的，还是随机从generation里抽两个人决斗，赢的人加进new_generation里，单元测试是在初期generation的基础上执行一次select，然后对比select前后的世代平均适应度gen_fitness_avr有没有下降，正常应该下降

### Crossover & Mutation

```python
def crossover(father: list, mather: list) -> list:
    cut0 = random.randint(0, city_num - 1)
    cut1 = random.randint(cut0, city_num - 1)
    child = [-1 for i in range(city_num)]
    for i in range(cut0, cut1 + 1):
        child[i] = father[i]
    for i in range(city_num):
        if i in range(cut0, cut1 + 1):
            continue
        for j in mather:
            if j not in child:
                child[i] = j
                break
    mp = random.random()
    if mp <= mutation_prob:
        mutation(child)
    return child


def mutation(child: list) -> list:
    return random.shuffle(child)

# crossover&mutation test -> pass
father = [i for i in range(city_num)]
mother = [i - 1 for i in range(city_num, 0, -1)]
child = crossover(father, mother)
print(child)
```

交配的算法之前已经介绍的比较清楚了，两个随机数cut0,cut1决定始末切点，子代DNA事前全部用填充-1的方式初期化，然后把前后切点内部的那段DNA从father的相应位置复制过来，之后遍历整个子代DNA，跳过刚才从father处复制的那些位点，对剩下的那些仍然填着-1的位点：遍历mother的DNA序列，如果有不存在于child的DNA序列里的值（即没有从father那里继承），就将那个值写进child当前遍历到的DNA位点里然后结束对mother的遍历，由于遍历mother是按照mother的DNA序列里的顺序遍历的，所以可以达成“除去来自father的那段DNA里的城市，剩下的城市按照mother的DNA序列里的顺序依次填入child里”的目的，每次产生child后立刻进入变异判定流程，这块也是直接流用之前的逻辑，产生一个0~1间的随机数并判定是否小于变异概率阈值mutation_prob，如果小于则判定命中，对child执行变异操作（完全随机重排）。针对Crossover的单元测试方法是手工指定两个互为倒序的DNA序列作为亲代，观察生成的子代是否符合设计的逻辑

### Iteration

```python
# iteration test
maps = map_init()
generation = generation_init()
for i in range(iter_num):
    print("iter_num=", i)
    gen_selected = select()
    gen_crossovered = []
    for j in range(population_size):
        father = random.choice(gen_selected)
        mother = random.choice(gen_selected)
        child = crossover(father, mother)
        gen_crossovered.append(child)
    generation = gen_crossovered
    fitlist = fitness_list()
    print(min(fitlist))
```
迭代操作基本也是流用之前的逻辑，与直接返回一个generation的select函数不同，crossover函数一次只返回一个child，所以需要使用设计的人口数的次数的循环来添加子代个体生成下一代generation。最后增加了打印迭代后的新generation里的最低fitness的语句，该值经过多次迭代后应会稳定在一个固定值，这个固定值就是最终的最优解（前提是整个进化流程没有问题）

其实到这里为止“解TSP”这个目标已经完成了，但是为了用户体验（作者装逼），我们来添加一个绘制每代最优fitness和世代平均fitness随迭代次数变化的图表的功能吧

### Draw Graph

```python
# graph test
maps = map_init()
generation = generation_init()
graph_min = []
graph_avr = []
for i in range(iter_num):
    print("iter_num=", i)
    gen_selected = select()
    gen_crossovered = []
    for j in range(population_size):
        father = random.choice(gen_selected)
        mother = random.choice(gen_selected)
        child = crossover(father, mother)
        gen_crossovered.append(child)
    generation = gen_crossovered
    fitlist = fitness_list()
    graph_min.append(min(fitlist))
    graph_avr.append(gen_fitness_avr())
plt.plot(graph_min, '-', label='min')
plt.plot(graph_avr, '-', label='avr')
plt.xlabel('iteration')
plt.ylabel('fitness')
plt.legend(loc='upper right')
plt.show()
```

首先在前面创建两个空列表graph_min和graph_avr用来装每次迭代后相应的数据，之后在每次迭代的最后把最小fitness和世代平均fitness添加进相应的列表里，最后是调用matplotlib画图表

其实到这里为止这个程序作为实验课题已经很完善了，但是为了多拿几分（作者装逼），我们来添加一个实时绘制每次迭代后得到的最短路径的动画的功能吧

### Draw Animation

```python
def best_route(fitlist: list) -> list:
    return generation[fitlist.index(min(fitlist))]


# animation test
maps = map_init()
generation = generation_init()
graph_map_x = [-1 for i in range(city_num)]
graph_map_y = [-1 for i in range(city_num)]
plt.ion()
for i in range(iter_num):
    print("iter_num=", i)
    gen_selected = select()
    gen_crossovered = []
    for j in range(population_size):
        father = random.choice(gen_selected)
        mother = random.choice(gen_selected)
        child = crossover(father, mother)
        gen_crossovered.append(child)
    generation = gen_crossovered
    plt.cla()
    fitlist = fitness_list()
    best = best_route(fitlist)
    for i in range(city_num):
        graph_map_x[i] = maps[best[i]][0]
        graph_map_y[i] = maps[best[i]][1]
    plt.plot(graph_map_x, graph_map_y, '-o')
    plt.draw()
    plt.pause(0.1)
```

首先需要找到最优路径的序列，由于之前已经有了储存所有路径的里程的列表fitlist了，所以只要找到fitlist里最小值所处的位置，然后照着这个位置从generation里调出相应位置的个体就可以了，best_route函数就是干这个的。其次在程序开始的地方创建两个列表graph_map_x和graph_map_y储存地图上每个城市的座标，之后用plt模块里的ion函数开启interactive mode，最后在每次迭代之后按照当次的最优路径的城市顺序把城市座标分别放进graph_map_x和graph_map_y里，然后按照这两个列表里存放的座标值画折线图，每次画完之后停顿0.1s防止动画速度过快

### Source Code Full Version

```python
import random
import math
import matplotlib.pyplot as plt

# initialize
map_size_x = 100
map_size_y = 100
maps = []
city_num = 10
generation = []
population_size = 100
mutation_prob = 0.05
iter_num = 50


def map_init():
    for i in range(city_num):
        city_x = random.randint(0, map_size_x)
        city_y = random.randint(0, map_size_y)
        maps.append([city_x, city_y])
    return maps


def generation_init():
    the_seed = []
    for i in range(city_num):
        the_seed.append(i)
    for i in range(population_size):
        random.shuffle(the_seed)
        generation.append(the_seed[:])
    return generation


# a debugging function to print generation in a cute format
def print_generation(generation: list):
    for i in range(population_size):
        print(i, generation[i])


def fitness(route: list) -> float:
    def distance(city0, city1):
        x0 = maps[city0][0]
        y0 = maps[city0][1]
        x1 = maps[city1][0]
        y1 = maps[city1][1]
        dis = math.sqrt((x0 - x1) ** 2 + (y0 - y1) ** 2)
        return dis

    distance_sum = 0
    for i in range(city_num - 1):
        distance_sum += distance(route[i], route[i + 1])
    return distance_sum


# a debugging function to get average fitness of generation
def gen_fitness_avr():
    fitness_sum = 0
    for i in range(population_size):
        fitness_sum += fitness(generation[i])
    return fitness_sum / population_size


def fitness_list():
    fitlist = []
    for route in generation:
        fitlist.append(fitness(route))
    return fitlist


def select():
    def tournament(route0: list, route1: list):
        if fitness(route0) < fitness(route1):
            return route0
        else:
            return route1

    new_generation = []
    for i in range(population_size):
        p0 = random.choice(generation)
        p1 = random.choice(generation)
        winner = tournament(p0, p1)
        new_generation.append(winner)
    return new_generation


def crossover(father: list, mather: list) -> list:
    cut0 = random.randint(0, city_num - 1)
    cut1 = random.randint(cut0, city_num - 1)
    child = [-1 for i in range(city_num)]
    for i in range(cut0, cut1 + 1):
        child[i] = father[i]
    for i in range(city_num):
        if i in range(cut0, cut1 + 1):
            continue
        for j in mather:
            if j not in child:
                child[i] = j
                break
    mp = random.random()
    if mp <= mutation_prob:
        mutation(child)
    return child


def mutation(child: list) -> list:
    return random.shuffle(child)


def best_route(fitlist: list) -> list:
    return generation[fitlist.index(min(fitlist))]


'''
def draw_best_route():
    fitlist1 = fitness_list()
    best = best_route(fitlist1)
    for i in range(city_num):
        graph_map_x[i] = maps[best[i]][0]
        graph_map_y[i] = maps[best[i]][1]
    # plt.plot(graph_map_x, graph_map_y, '-o')
    plt.draw()
'''

# map init test -> pass
'''
maps = map_init()
for i in range(city_num):
    print(maps[i])
'''
# generation init test -> pass
'''
maps = map_init()
generation = generation_init()
for i in range(population_size):
    print(i, generation[i])
'''
# fitness test -> pass
'''
maps = map_init()
generation = generation_init()
for i in range(population_size):
    print(i, fitness(generation[i]))
'''
# select test -> pass
'''
maps = map_init()
generation = generation_init()
print(gen_fitness_avr(generation))
gen_selected = select()
print(gen_fitness_avr(gen_selected))
'''
# crossover&mutation test -> pass
'''
father = [i for i in range(city_num)]
mother = [i - 1 for i in range(city_num, 0, -1)]
child = crossover(father, mother)
print(child)
'''
# iteration test -> pass
'''
maps = map_init()
generation = generation_init()
for i in range(iter_num):
    print("iter_num=", i)
    gen_selected = select()
    gen_crossovered = []
    for j in range(population_size):
        father = random.choice(gen_selected)
        mother = random.choice(gen_selected)
        child = crossover(father, mother)
        gen_crossovered.append(child)
    generation = gen_crossovered
    fitlist = fitness_list()
    print(min(fitlist))
    # print(gen_fitness_avr())
'''
# graph test -> pass
'''
maps = map_init()
generation = generation_init()
graph_min = []
graph_avr = []
for i in range(iter_num):
    print("iter_num=", i)
    gen_selected = select()
    gen_crossovered = []
    for j in range(population_size):
        father = random.choice(gen_selected)
        mother = random.choice(gen_selected)
        child = crossover(father, mother)
        gen_crossovered.append(child)
    generation = gen_crossovered
    fitlist = fitness_list()
    graph_min.append(min(fitlist))
    graph_avr.append(gen_fitness_avr())
plt.plot(graph_min, '-', label='min')
plt.plot(graph_avr, '-', label='avr')
plt.xlabel('iteration')
plt.ylabel('fitness')
plt.legend(loc='upper right')
plt.show()
'''

# animation test -> pass
maps = map_init()
generation = generation_init()
graph_min = []
graph_avr = []
graph_map_x = [-1 for i in range(city_num)]
graph_map_y = [-1 for i in range(city_num)]
plt.ion()
for i in range(iter_num):
    print("iter_num=", i)
    gen_selected = select()
    gen_crossovered = []
    for j in range(population_size):
        father = random.choice(gen_selected)
        mother = random.choice(gen_selected)
        child = crossover(father, mother)
        gen_crossovered.append(child)
    generation = gen_crossovered

    plt.cla()
    fitlist = fitness_list()
    best = best_route(fitlist)
    for i in range(city_num):
        graph_map_x[i] = maps[best[i]][0]
        graph_map_y[i] = maps[best[i]][1]
    plt.plot(graph_map_x, graph_map_y, '-o')
    plt.draw()
    plt.pause(0.1)
```

动画和图表暂时没实现共存，要么迭代过程中出动画，要么迭代结束后出图表，可以根据需求调整注释范围选择使用哪段test代码

最后放一个程序运行时的录屏动画吧

[Screencast](http://7xt9ka.com2.z0.glb.qiniucdn.com/Screencast.webm)
