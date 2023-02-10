1987年Craig W.Reynolds发表一篇名为[《鸟群、牧群、鱼群：分布式行为模式》](http://www.red3d.com/cwr/boids/)的论文，描述了一种非常简单的、以面向对象思维模拟群体类行为的方法，称之为 Boids ，Boids 采用了三个核心的规则：
- 同向性(Alignment)：趋向与邻近的个体采用相同的速度方向
- 排斥性(Separation)：避免与群体内邻近个体发生碰撞
- 凝聚向心性(Cohesion)：向邻近个体的平均位置靠近

## 核心规则伪代码实现
[Flock Simulation using Boids in Unity](https://blog.yarsalabs.com/flock-simulation-using-boids-in-unity/)
### 同向性
```csharp
center=(boid1.position+boid2.position+boid3.position+...)/numberOfBoids
direction=center-currentPosition
```

### 排斥性
```csharp
awayVector= vectorZero-(boid1.position-current.Position)-(boid2.Position-currentPosition)-(boid3.position-currentPosition)-...
```


https://www.cnblogs.com/SouthBegonia/p/10975851.html