---
layout: article
title: "Creating a virtual controller with Stack on Flame"
date: 2019-09-02
author: Erick
---

One thing that a lot of people asks us, is how to create a virtual gamepad for the input your game. Of course there are a lot of solutions for that, you could for example render it in game, and handle the touch events yourself (which is how we do on [Tales of a lost mine](/games/tales.html)), but one nice solution that I have found recently is to use Flutter's `Stack` widget.

So, what does the `Stack` widget does? It is a basic structure widget that lets you "stack" widgets on top of which other, that works very well for what we are trying to make, since our virtual gamepad will stay above the game itself.

In this article we will be building something very simple, we will have a Flame game with a white square on the screen, where the player can use a "left" and "right" button to move the player, by the end of the article we should have something like this:

<img src="/assets/imgs/articles/stack-controls/final.gif"/>


## Lets code!

First things first, lets create our simple game, nothing too fancy here, we just create a `Player` component that moves on the `x` axis given a direction, and a `BaseGame` instance which just creates the player, adds it to the game, and declare some basic methods that will change the direction of our player.

```dart
class Player extends PositionComponent {
  double direction = 0.0;
  static final _white = Paint()..color = const Color(0xFFFFFFFF);

  Player() {
    width = height = 100;
    y = 50.0;
  }

  @override
  void update(double dt) {
    x += 200 * dt * direction;
  }

  @override
  void render(Canvas canvas) {
    canvas.drawRect(toRect(), _white);
  }
}

class MyGame extends BaseGame {
  Player _player;

  void stopMoving() { _player.direction = 0; }

  void movingRight() { _player.direction = 1; }

  void movingLeft() { _player.direction = -1; }

  MyGame() {
    _player = Player();
    add(_player);
  }
}
```

Next, lets create our gamepad buttons, bellow is the source of a very basic widget which will represent our button, it basically renders a grey box, and wraps itself inside a `GestureDetector` so we can handle tap events:

```dart
class GameButton extends StatelessWidget {
  final String label;
  final void Function(TapUpDetails) onTapUp;
  final void Function(TapDownDetails) onTapDown;

  GameButton({this.label, this.onTapDown, this.onTapUp});

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
        onTapUp: onTapUp,
        onTapDown: onTapDown,
        child: SizedBox(
            width: 100,
            height: 100,
            child: Container(
                color: const Color(0xFFe5e5e5),
                child: Center(child: Text(label)))));
  }
}

```

Finally the "magic" part, we create a `Widget` which will build the structure using `Stack` so we can  have the desired effect of the buttons above the game.

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final game = MyGame();
    return MaterialApp(
        home: Scaffold(
            body: Container(
                color: const Color(0xFF000000),
                child: Stack(children: [
                  game.widget,
                  Positioned(
                      left: 10,
                      bottom: 10,
                      child: GameButton(
                          onTapUp: (_) {
                            game.stopMoving();
                          },
                          onTapDown: (_) {
                            game.movingLeft();
                          },
                          label: 'left')),
                  Positioned(
                      right: 10,
                      bottom: 10,
                      child: GameButton(
                          onTapUp: (_) {
                            game.stopMoving();
                          },
                          onTapDown: (_) {
                            game.movingRight();
                          },
                          label: 'right'))
                ]))));
  }
}
```

The key widgets here are the `Stack` and the `Positioned`, when we just add a widget to the `Stack`, it will be rendered on the top of the `Stack` without any proper position, by wrapping a widget with a `Positioned` component we can set where it should be positioned, and that is what we do with the `GameButton` widget, positioning each one on each bottom corner of the screen.

This is very simple example, just to show you the basics how a virtual gamepad could be achieved, and could be expanded to have better looks and fell, the `GameButton` component could be turned into a `StatefulWidget` for example, to have a different look when it is pressed and so on, the sky is the limit.

The complete source for this example can be found [here](https://github.com/fireslime/flame-examples/tree/master/stack_controls).
