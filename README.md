Alright, here's a simple shooter game written in Java using the Swing library:

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;

public class ShooterGame extends JPanel implements ActionListener {
    private Timer timer;
    private Player player;
    private ArrayList<Enemy> enemies;
    private boolean isGameRunning;
    private int score;

    public ShooterGame() {
        setPreferredSize(new Dimension(400, 400));
        setBackground(Color.BLACK);
        setFocusable(true);

        player = new Player();
        enemies = new ArrayList<>();
        isGameRunning = true;
        score = 0;

        timer = new Timer(10, this);
        timer.start();

        addKeyListener(new KeyAdapter() {
            @Override
            public void keyPressed(KeyEvent e) {
                if (e.getKeyCode() == KeyEvent.VK_LEFT) {
                    player.moveLeft();
                } else if (e.getKeyCode() == KeyEvent.VK_RIGHT) {
                    player.moveRight();
                } else if (e.getKeyCode() == KeyEvent.VK_SPACE) {
                    player.shoot();
                }
            }
        });
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        player.draw(g);
        for (Enemy enemy : enemies) {
            enemy.draw(g);
        }
        g.setColor(Color.WHITE);
        g.drawString("Score: " + score, 10, 20);
    }

    public void actionPerformed(ActionEvent e) {
        if (isGameRunning) {
            player.update();
            for (int i = 0; i < enemies.size(); i++) {
                Enemy enemy = enemies.get(i);
                enemy.update();
                if (enemy.getY() >= getHeight()) {
                    enemies.remove(i);
                    i--;
                } else if (enemy.intersects(player)) {
                    isGameRunning = false;
                    JOptionPane.showMessageDialog(this, "Game over! Your score is: " + score);
                    break;
                }
            }
            if (Math.random() < 0.03) {
                enemies.add(new Enemy(getWidth()));
            }
            score++;
            repaint();
        }
    }

    private class Player {
        private int x, y, dx, width, height;
        private ArrayList<Bullet> bullets;

        public Player() {
            x = 200;
            y = 350;
            dx = 0;
            width = 30;
            height = 30;
            bullets = new ArrayList<>();
        }

        public void moveLeft() {
            dx = -5;
        }

        public void moveRight() {
            dx = 5;
        }

        public void shoot() {
            bullets.add(new Bullet(x + width / 2, y));
        }

        public void update() {
            x += dx;
            if (x < 0) {
                x = 0;
            } else if (x + width > getWidth()) {
                x = getWidth() - width;
            }
            for (int i = 0; i < bullets.size(); i++) {
                Bullet bullet = bullets.get(i);
                bullet.update();
                if (bullet.getY() < 0) {
                    bullets.remove(i);
                    i--;
                } else {
                    for (int j = 0; j < enemies.size(); j++) {
                        Enemy enemy = enemies.get(j);
                        if (bullet.intersects(enemy)) {
                            bullets.remove(i);
                            i--;
                            enemies.remove(j);
                            j--;
                            score += 10;
                            break;
                        }
                    }
                }
            }
            dx = 0;
        }

        public void draw(Graphics g) {
            g.setColor(Color.BLUE);
            g.fillRect(x, y, width, height);
            for (Bullet bullet : bullets) {
                bullet.draw(g);
            }
        }
    }

    private class Enemy {
        private int x, y, dy, width, height;

        public Enemy(int maxX) {
            x = (int) (Math.random() * maxX);
            y = 0;
            dy = 2;
            width = 20;
            height = 20;
        }

        public void update() {
            y += dy;
        }

        public int getY() {
            return y;
        }

        public boolean intersects(Rectangle r) {
            return new Rectangle(x, y, width, height).intersects(r);
        }

        public void draw(Graphics g) {
            g.setColor(Color.RED);
            g.fillOval(x, y, width, height);
        }
    }

    private class Bullet {
        private int x, y, dy, width, height;

        public Bullet(int x, int y) {
            this.x = x;
            this.y = y;
            dy = -5;
            width = 5;
            height = 10;
        }

        public void update() {
            y += dy;
        }

        public int getY() {
            return y;
        }

        public boolean intersects(Rectangle r) {
            return new Rectangle(x, y, width, height).intersects(r);
        }

        public void draw(Graphics g) {
            g.setColor(Color.YELLOW);
            g.fillRect(x, y, width, height);
        }
    }

    public static void main(String[] args) {
        JFrame frame = new JFrame("Shooter Game");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.getContentPane().add(new ShooterGame());
        frame.pack();
        frame.setLocationRelativeTo(null);
        frame.setVisible(true);
    }
}
```

This game is a simple shooter game where the player controls a blue square and tries to shoot down red circles that fall from the top of the screen. The player can move left and right using the arrow keys, and shoot bullets using the space bar. The game ends if an enemy circle intersects the player square, and the score is displayed at the top of the screen.
