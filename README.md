# pinball
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.awt.image.BufferedImage;
import javax.sound.sampled.AudioInputStream;
import javax.sound.sampled.AudioSystem;
import javax.sound.sampled.Clip;
import java.io.File;
class Bricks {
    public int set[][];
    public int brickwidth;
    public int brickheight;
    public int mh=3;
    public Bricks(int row, int col) {
        set = new int[row][col];
        for (int i = 0; i < set.length; i++) {
            for (int j = 0; j < set[i].length; j++) {
                set[i][j] =3;
            }
        }
        brickwidth = 540 / col;
        brickheight = 150 / row;
    }

    public void draw(Graphics2D g) {
        for (int i = 0; i < set.length; i++) {
            for (int j = 0; j < set[i].length; j++) {
                if (set[i][j] > 0) {
                    g.setColor(Color.ORANGE);
                    g.fillRect(j * brickwidth + 80, i * brickheight + 50, brickwidth, brickheight);
                    g.setStroke(new BasicStroke(3));
                    g.setColor(Color.black);
                    g.drawRect(j * brickwidth + 80, i * brickheight + 50, brickwidth, brickheight);
                   g.setColor(Color.WHITE);
                g.setFont(new Font("serif", Font.BOLD, 12));
                g.drawString(Integer.toString(set[i][j] ), j * brickwidth + 80 + brickwidth / 2 - 5, i * brickheight + 50 + brickheight / 2 + 5);
                }
            }
        }
    }

    public void setBlockValue(int value, int row, int col) {
        set[row][col] = value;
    }
}
public class PinBallmodifications extends JFrame
{
private Frame frame = new Frame(" Pinball games ");
// Window width
private final int WIDTH = 700;
// Window height
private final int HEIGHT = 600;
// The width and height of the board
private final int RACKET_WIDTH = 100;
private final int RACKET_HEIGHT = 8;
// Record the coordinates of the board
private int racketX = 310;
// The board can only move horizontally , Cannot move vertically
private final int racketY = 550;
// The size of the ball , That is, the diameter and length of the circle
private final int BALL_SIZE = 20;
// Record the coordinates of the ball
private int ballX = 120;
private int ballY = 350;
// Record the ball in x and y The speed of movement in the axial direction
private int speedX = 5;
private int speedY = 10;
// Identifies whether the game is over ,true For the end
private boolean isOver = false;
// Declare timer
private Timer timer;
//CODE MODIFIED
private Bricks set;
private int score = 0;
    private int bricks = 28;
private JButton pauseButton;
    private boolean isPaused = false;
private int delay; // Default delay
    private Color ballColor = Color.PINK; // Default ball color


    private void selectLevel() {
        String[] levels = {"Easy", "Medium", "Hard"};
        String selectedLevel = (String) JOptionPane.showInputDialog(this, "Select Level", "Level Selection",
                JOptionPane.QUESTION_MESSAGE, null, levels, levels[0]);
    
        if (selectedLevel != null) {
            switch (selectedLevel) {
                case "Easy":
                    set = new Bricks(3, 7);
                    bricks = 21;
                    delay = 50;
                    break;
                case "Medium":
                    set = new Bricks(4, 7);
                    bricks = 28;
                    delay = 40;
                    break;
                case "Hard":
                    set = new Bricks(5, 7);
                    bricks = 35;
                    delay = 30;
                    break;
                default:
                    // Default to Medium level
                    set = new Bricks(4, 7);
                    bricks = 28;
                    delay = 40;
            }
        } else {
            // Default to Medium level if the user closes the dialog
            set = new Bricks(4, 7);
            bricks = 28;
            delay = 40;
        }

    }
    
    private void selectColor() {
        String[] colors = {"Pink", "White", "Yellow", "Magenta"};
        String selectedColor = (String) JOptionPane.showInputDialog(this, "Select Color of Ball", "Color Selection",
                JOptionPane.QUESTION_MESSAGE, null, colors, colors[0]);
    
        if (selectedColor != null) {
            switch (selectedColor.toLowerCase()) {
                case "pink":
                    ballColor = Color.PINK;
                    break;
                case "white":
                    ballColor = Color.WHITE;
                    break;
                case "yellow":
                    ballColor = Color.YELLOW;
                    break;
                case "magenta":
                    ballColor = Color.MAGENTA;
                    break;
                default:
                    ballColor = Color.PINK; // Default to pink color
            }
        } else {
            ballColor = Color.PINK; // Default to pink color if the user closes the dialog
        }
    }
// Custom class , Inherit canvas, Act as a Sketchpad
private class MyCanvas extends JPanel{
    
@Override
public void paint(Graphics g)
 {

super.paint(g);
        g.setColor(Color.BLACK);
        g.fillRect(1, 1, 692, 592);
        set.draw((Graphics2D) g);
        g.setColor(Color.YELLOW);
        g.fillRect(racketX,575, 100, 8);
        g.setColor(ballColor); // Set the color of the ball
        g.fillOval(ballX, ballY, 20, 20);
//score presentation
        g.setFont(new Font("serif", Font.BOLD, 25));
        g.drawString("score :" + score, 575, 30);


//winning the game
        if (bricks <= 0) {
            isOver = false;
            speedX = 0;
            speedY = 0;
            g.setColor(Color.WHITE);
            g.setFont(new Font("serif", Font.BOLD, 30));
            g.drawString("YOU WON ", 250, 100);
            g.setFont(new Font("serif", Font.BOLD, 20));
            g.drawString("Press Enter to restart: ", 250, 300);
 
        }
//loosing the game
        if (ballY> 570) {
            isOver = false;
            speedX = 0;
            speedY = 0;
            g.setColor(Color.WHITE);
            g.setFont(new Font("serif", Font.BOLD, 30));
            g.drawString("game over, score: " + score, 250, 100);
            g.setFont(new Font("serif", Font.BOLD, 20));
            g.drawString("Press Enter to restart: ", 250, 300);
              
        }

    }
}

// Create a painting area
MyCanvas drawArea = new MyCanvas();
public void init(){

// Complete the change of racket coordinates
KeyListener listener = new KeyAdapter() {
@Override
public void keyPressed(KeyEvent e) {
int key = e.getKeyCode();
        if (key == KeyEvent.VK_RIGHT) {
            if (racketX >= 600) {
                isOver = true;
                 racketX= 600;
            } else {
                isOver = true;
                racketX += 25;
            }
        }
        if (key == KeyEvent.VK_LEFT) {
            if (racketX < 10) {
                isOver= true;
                racketX= 10;
            } else {
                 isOver = true;
                racketX -= 25;
            
            }
        }
        if (key == KeyEvent.VK_ENTER) {
            ballX = 120;
            ballY = 350;
            speedX = 5;
            speedY = 10;
            racketX = 310;
            score = 0;
            selectLevel();
            selectColor(); // Prompt for color selection on restart
            isOver= true;
            frame.repaint();
        }
}
};


// to frame Windows and drawArea Painting area registration listener
frame.addKeyListener(listener);
drawArea.addKeyListener(listener);
// Control of spherical coordinates
ActionListener task = new ActionListener() {
@Override
public void actionPerformed(ActionEvent e) {
timer.start();
// racket intersection with ball to bounce back
        if (isOver) {
            if (new Rectangle(ballX, ballY, BALL_SIZE, BALL_SIZE).intersects(new Rectangle(racketX, 550, 100, 8))) {
               speedY = -speedY;
            }
//loop for bricks
            A: for (int i = 0; i < set.set.length; i++) {
                for (int j = 0; j < set.set[i].length; j++) {
                    if (set.set[i][j] >0) {
                      //dimensions of the brick
                       
                        int brickx = j * set.brickwidth + 80;
                        int bricky = i * set.brickheight + 50;
                        int brickwidth = set.brickwidth;
                        int brickheight = set.brickheight;
                        Rectangle r = new Rectangle(brickx, bricky, brickwidth, brickheight);
// Create rectangles for the ball and the current brick

                        Rectangle r1 = new Rectangle(ballX, ballY,BALL_SIZE, BALL_SIZE);
                        Rectangle r2 = r;
// Check if the ball intersects with the current brick
                        if (r1.intersects(r2)) {
                             set.set[i][j]--;
                            
                         if(set.set[i][j]==0)
                        {    bricks--;
                            score = score + 5;
                            set.setBlockValue(0, i, j);
                         }
// Determine the direction to reflect the ball based on the intersection point
                            if (ballX + 19 <= r2.x || ballY + 1 >= r2.width) {
                                speedX = -speedX;
                            } else {
                                speedY = -speedY;
                            }
                            break A;// Exit the outer loop, as we've handled the collision with one brick
                        }
                    }
                }
            }
// Update the spherical coordinates , Redraw picture
// Correct the positive and negative velocity according to the boundary range
// When the ball touches the left and right boundaries, modify the direction of motion of the ball
if(ballX <= 0 || ballX >= (WIDTH - BALL_SIZE)){
speedX = -speedX;// Move in the opposite direction
}
// When the ball touches the upper boundary or below the horizontal line of the board and does not touch the board, change the direction of motion of the ball
if(ballY <= 0 || (ballY > racketY - BALL_SIZE && ballX > racketX && ballX < racketX + RACKET_WIDTH)){
speedY = -speedY;// Move in the opposite direction
}
if(ballY > racketY - BALL_SIZE && (ballX < racketX || ballX > racketX + RACKET_WIDTH)){
if(ballY == HEIGHT){// When the ball falls to the bottom position, it stops the game
// The current ball is beyond the horizontal line of the racket and beyond the width of the racket , Game over
timer.stop();// Stop timer
System.out.println(" The game has finished running !");
// Modify the game to mark the end
isOver = true;
// Redraw picture
drawArea.repaint();
}
}
// Update the coordinates of the ball
ballX += speedX;
ballY += speedY;
// Redraw the interface
drawArea.repaint();
}
}
};

// Set the size of the sketchpad
timer = new Timer(delay,task);
        timer.setDelay(delay); // Update timer delay
timer.start();
 
drawArea.setPreferredSize(new Dimension(700, 600));
frame.add(drawArea);
frame.requestFocusInWindow();
frame.setFocusable(true);
frame.setLocation(700,50);// The window position is centered
frame.pack();// Best size
frame.setVisible(true);// Window display
// Click on the X End operation
frame.addWindowListener(new WindowAdapter() {
@Override
public void windowClosing(WindowEvent e) {
System.out.println(" The game has finished running !");
System.exit(0);
}
});
}

public static void main(String[] args) {
PinBallmodifications pm=new PinBallmodifications();
pm.selectLevel();
        pm.selectColor();
pm.init();
}
}
