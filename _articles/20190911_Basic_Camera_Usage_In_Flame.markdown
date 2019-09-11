---
layout: article
title: "Basic Camera usage on Flame"
date: 2019-09-11
author: Erick
---

Camera, a simple yet heavly used resource on game dev, it is essential for building games that have its "world" bigger than the screen of the device it is been played on.

There are a lot of ways to make a camera, and it can have many different features, it can rotate, it can be used to look up or down, etc. On this article I will show you how to create a very basic kind of camera, a camera that just moves alongside with the player.

For this, we will be building a very simple game, composed only by solid geometric forms, we will have a white rectangle for our ground platform, a blue rectangle for our player and a bunch o grey squares for random background objects and on this game, if you touch on the left side of the screen, player moves left, if touching the right side, player moves right.

<img src="/assets/imgs/articles/simple-camera/final.gif"/>

For this example we will be using Flame's `BaseGame` class, which already provides a simple implementation of a camera, on it, you will find a property called `camera` which is an instance of the `Position` class, that property will tell the game how much the camera needs to be moved. By default the camera starts at the `(0.0, 0.0)` position and a important thing to remember is that Flutter's position system is based on a top-left orientation, meaning that the `(0.0, 0.0)` position of the canvas is the top left corner of the screen.

## Let's code

On this article, I will omit most of the unimportant parts of the code and will focus only on what is about the camera. You can see the full working example [here](https://github.com/fireslime/flame-examples/tree/master/simple_camera)

```dart
class Player extends PositionComponent {
  static final _blue = Paint()..color = Color(0xFF0000FF);
  static const double SPEED = 300;

  Player(this.gameRef);

  // Keeps a game reference,
  // so we can update the camera alonside our movement
  final SimpleCameraGame gameRef;

  int _direction = 0;

  void moveRight() { _direction = 1; }
  void moveLeft() { _direction = -1; }
  void stopMoving() { _direction = 0; }

  @override
  void update(double dt) {
    // Calculates how much we are going to move on this iteration
    final step = _direction * SPEED * dt;

    // Moves me
    x += step;

    // Moves the camera by the same amount
    gameRef.camera.x += step;
  }

  @override
  void render(Canvas canvas) {
    canvas.drawRect(toRect(), _blue);
  }
}

class SimpleCameraGame extends BaseGame {
  Player player;

  SimpleCameraGame(Size screenSize) {
    size = screenSize;
    
    // creates the platform and background objects
    // ...omited...

    // Creates the player
    add(
        player = Player(this)
        ..x = 100
        ..y = screenSize.height - 220
        ..width = 50
        ..height = 100
    );
  }
}
```

That is basic it, by adding the same amount of the movement that the player will move to the camera, it will now follow the player forever. This is a very basic example, but I guess this can give you the understand needed to use Flame's camera, and from this as a starting point, create more complex cameras!

I hope that you found this usefull and if you still have doubts about how to use the camera, or any other thing, come talk to us on our [Discord server](https://discord.gg/jzM7vPP).
