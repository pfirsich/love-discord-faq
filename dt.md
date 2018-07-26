# Framerate Independent Simulation
## Short Version
### Motivation
A very common line in a lot of game code is something like this:
```lua
player.position.x = player.position.x + player.velocity.x
```
Assuming your game runs at a comfortable `60 fps` on your development machine, this line is executed 60 times per second, i.e. every `~16.67 ms`. Now when I try it out on my computer, your game may run at for example `120 fps`. That line is then executed 120 times per second - twice as often! And the player in turn moves twice as fast.

To fix this undesirable behaviour, we can use the variable `dt`, that is passed to [`love.update`](https://love2d.org/wiki/love.update) and contains the amount of time that has passed since `love.update` was last called (i.e. about the time that it took to process the last frame):
```lua
player.position.x = player.position.x + player.velocity.x * dt
```
This way if my framerate is twice as high as yours, `dt` will be half of what it is for you and I need to execute the same line twice to move the same amount (since each time it is only moved half as much)!

Keep in mind that you have to adjust `player.velocity.x`! If your framerate while deciding on the value of that constant was e.g. `60 fps` (a frame time of `dt = 1s/60 = ~16.67 ms` you need to multiply your constant with `60` to get the new constant (so at `60 fps` those factors will cancel out to `1`).

## Details
### The Mathematical Problem
The problem we are trying to solve here is the numerical integration of an ordinary differential equation. Our multiplication by `dt` is not the only solution here! The solution I showed above, if extended for velocity/position integration in this way (most common):

```lua
player.velocity.x = player.velocity.x + player.speed.x * dt
player.position.x = player.position.x + player.velocity.x * dt
```

is called the [Semi-Implicit Euler Method](https://en.wikipedia.org/wiki/Semi-implicit_Euler_method) or Euler-Cromer algorithm. It is a symplectic method, which  means that this integrator preserves phase space volume, which improves energy conservation and therefore the stability of the integration. It is not a very accurate method though, since it is of first order only - i.e. linear. Please note that this method as outline above is very easy to turn into a Explicit Euler (by accident), which is not symplectic or stable!

Depending on the use-case other integration operators are desired, like for example [Verlet Integration](https://en.wikipedia.org/wiki/Verlet_integration), [Leap-frog Integration](https://en.wikipedia.org/wiki/Leapfrog_integration) or [Runge-Kutta methods](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods).

### Deterministic Physics
If your goal is to have your physics be framerate independent and behave the same on every different framerate as much as possible (e.g. for networked multiplayer games), you need to try to keep `dt` very similar (or the same). Our integration is just a numerical approximation and can only be accurate in an infinite number of steps, which is highly impractical. The error that is introduced by our approximation is dependent on `dt`, so that we need to "fix" it. For specifics see this iconic article by Glenn Fiedler:

[Fix Your Timestep!](https://gafferongames.com/post/fix_your_timestep/)

### Multiplication
If you want a quantity to decay exponentially over time, you will probably write something like this in your `love.update` function:
```lua
value = value * factor
```
With for example `factor = 0.5`, this will result in your value to halve every frame, making it fall off like `value(frame) = exp(ln(factor) * frame)`.

If you wish to fix this in regards to framerate independence, you need to correct it like so:
```lua
value = value * math.exp(ln(factor) * dt/origDt)
```
With `oridDt` being the `dt` you chose `factor` with.
