---
layout: post
title:  "Random Walks in Julia"
date:   2019-08-10
---

Use the Julia library Luxor to generate some random walk graphs with turtles.


```julia
using Luxor
```

## Random walk with up-down-left-right with equal probability


```julia
Drawing(600, 400, "images/random_walk_4equal.png")
origin()
background("midnightblue")

let 🐢=Turtle()
    Pencolor(🐢, "cyan")
    Penwidth(🐢, 1.5)

    for i in 1:1e5
        choice = rand(1:4)
        if choice == 1
            # Go left
            Orientation(🐢, 180)
            Forward(🐢, 1)
        elseif choice == 2
            # Go right
            Orientation(🐢, 0)
            Forward(🐢, 1)
        elseif choice == 3
            # Go up
            Orientation(🐢, 270)
            Forward(🐢, 1)
        elseif choice == 4
            # Go down
            Orientation(🐢, 90)
            Forward(🐢, 1)
        end
    end
    finish()
end;
```

![](/assets/random_walk/random_walk_4equal.png)

## Random walk with up-down-left-right with bias down and right


```julia
Drawing(600, 400, "images/random_walk_bias.png")
origin()
background("midnightblue")

let 🐢=Turtle()
    Pencolor(🐢, "cyan")
    Penwidth(🐢, 1.5)
    
    for i in 1:1e5
        choice = rand(1:100)
        if choice <= 24
            # Go left, 24% chance
            Orientation(🐢, 180)
            Forward(🐢, 1)
        elseif choice <= 48
            # Go up, 24% chance
            Orientation(🐢, 270)
            Forward(🐢, 1)
        elseif choice <= 74
            # Go right, 26% chance
            Orientation(🐢, 0)
            Forward(🐢, 1)
        else
            # Go down, 26% chance
            Orientation(🐢, 90)
            Forward(🐢, 1)
        end
    end
    finish()
end;
```

![](/assets/random_walk/random_walk_bias.png)

## Random walk with all degrees of freedom


```julia
Drawing(600, 400, "images/random_walk_freedom.png")
origin()
background("midnightblue")

let 🐢=Turtle()
    Pencolor(🐢, "cyan")
    Penwidth(🐢, 1.5)

    for i in 1:1e5
        degrees = rand(1:360)
        Orientation(🐢, degrees)
        Forward(🐢, 1)
    end
    finish()
end;
```

![](/assets/random_walk/random_walk_freedom.png)

Find the [notebook on GitHub](https://github.com/suzil/julia-notebooks/blob/master/Random%20Walk.ipynb).
