//Fruit-Catcher-Game
//==================
import java.awt.*;
import java.awt.event.*;
import javax.swing.*;
import java.util.*;
import  sun.audio.*;
import java.io.*;

class Fruitcatcher extends JFrame implements Runnable,KeyListener
{        
  JPanel p1,p2;
  JButton bs ;
  JMenuBar mymbar;
  JMenu game,help;
  Thread myt;
  JTextArea t;
  int score=0,x=700,y=600,directionx=50,directiony=0,bsx=300,bsy=550,i=-1,speed =10,cntr=0,n=1,m1=0,m2=0,m3=0;
  JButton[] fr = new JButton[40];
  Random r = new Random();
  Point [] frp = new Point[40];
  int[] frx = new int[40];
  int[] fry = new int[40];
  boolean flag=false,flag1=true;
  Image img1;
  Toolkit toolkit=Toolkit.getDefaultToolkit();
  AudioPlayer MGP = AudioPlayer.player;
  ContinuousAudioDataStream loop = null;

Fruitcatcher()
{
super("Fruitcatcher");
setSize(700,700);
createbar();
t = new JTextArea("Score ==>" + score);
t.setEnabled(false);
t.setBackground(Color.BLACK);
img1 = toolkit.getImage("Start.JPEG");
p1 = new BackgroundPanel();
p2 = new JPanel();
p1.setLayout(null);
p1.setVisible(true);
p2.setLayout(new GridLayout(0, 1));
p1.setBounds(0, 0, x, y);
p2.setBounds(0, y, x, 30);
p2.add(t); // will contain score board
        
// end of UI design

getContentPane();
getContentPane().setLayout(null);
getContentPane().add(p1);
getContentPane().add(p2);
show();
setDefaultCloseOperation(EXIT_ON_CLOSE);
addKeyListener(this);
myt = new Thread(this);

}

public void initializeValues()
{
score = 0;
directionx=50;
directiony=0;
bsx=300;
bsy=550;
i = -1;
}


public void paint(Graphics g)
{
  super.paint(g);
  if(flag1)
  g.drawImage(img1,0,50,this);
}


public void createBasket()
{                                                                     
 bs = new JButton();
 bs.setEnabled(true);
 p1.add(bs);
 bs.setBounds(bsx, bsy, 50, 50);
 ImageIcon img = new ImageIcon("b11.jpg");
 bs.setIcon(img);
 bs.setBackground(Color.green);
}

public void createbar()
{  
    mymbar = new JMenuBar();
    game = new JMenu("Game");
    JMenuItem newgame = new JMenuItem("New Game");
    JMenuItem exit = new JMenuItem("Exit");
    newgame.addActionListener(new ActionListener()
    {
       public void actionPerformed(ActionEvent e)
       {
         reset();
        }
      });
     exit.addActionListener(new ActionListener()
     {
       public void actionPerformed(ActionEvent e)
        {
           System.exit(0);
         }
      });
 
    game.add(newgame);
    game.addSeparator();
    game.add(exit);
 
    mymbar.add(game);
 
        
    help = new JMenu("Help");
 
    JMenuItem creator = new JMenuItem("Creator");
    JMenuItem inst = new JMenuItem("Instruction");   
      
     creator.addActionListener(new ActionListener() 
      {
         public void actionPerformed(ActionEvent e)
          {
            JOptionPane.showMessageDialog(p2, "Name :SUNNY RAJKUMAR NANDWANI");
          }
       });
      inst.addActionListener(new ActionListener() 
      {
         public void actionPerformed(ActionEvent e)
          {
            JOptionPane.showMessageDialog(p2, "Move the basket left & right by the respective arrow keys so as to collect the falling fruits which correspondingly increase the score. Game get over if one miss to collect the fruit");
          }
       });
 
        help.add(creator);
        help.add(inst);      
        mymbar.add(help);
 
        setJMenuBar(mymbar);
}

public void keyPressed(KeyEvent e)
{
        // Basket move to left when player pressed left arrow
        if (e.getKeyCode() == 37)
          {
            directionx = -50; // means basket move right to left by 50pixels
            directiony = 0;
            flag =true;
           }
        // basket move to right when player pressed right arrow
        if (e.getKeyCode() == 39)
         {
            directionx = +50; // means basket move from left to right by 10 pixel
            directiony = 0;
            flag = true;
          }
}

public void keyReleased(KeyEvent e)
{
   }
 
public void keyTyped(KeyEvent e)
{
   }

public void move()
{
flag1=false;
repaint();
if(flag)
{
flag=false;
 bsx += directionx;
 bsy += directiony;
 bs.setBounds(bsx, bsy, 50, 50);
 if (bsx==x)
{
   bsx = 0;
 } 
else if (bsx == 0)
{
   bsx = x - 50;
}

}
if(cntr==150)
{
cntr=0;
int a = ((r.nextInt(700))/50)*50;
if(i<40)
{i++;
}
else if(i==40)
{
i=0;
}
if(a!=0||a!=650)
{
fr[i] = new JButton();
fr[i].setEnabled(true);
fr[i].setBounds(a,0,50,50);
p1.add(fr[i]);
ImageIcon img1 = new ImageIcon("Fruit1.JPEG");
fr[i].setIcon(img1);
}
frp[i]=fr[i].getLocation();
frx[i]=a;
fry[i]=0;
for (int c = 0;  c<i; c++)
        {
              fry[c]+=50;
              fr[c].setBounds(frx[c],fry[c],50,50);
              
        }
for (int c = 0; c < i; c++)
{
  if (frx[c]==bsx && fry[c]== bsy)
     {
        
        score+=50;
        t.setText("Score ==>" + score);
       }
  else if(frx[c]!=bsx && fry[c]== bsy)
      {
                t.setText("GAME OVER    " + score);
                MGP.stop(loop);
                JOptionPane.showMessageDialog(p2, "GAME OVER!\nSCORE :"+score);
                try
               {
                    myt.join();
                }
                 catch (InterruptedException e)
                 {
                }
               break;
        }
}
p1.repaint();
show();
}
 }

void music()
{
AudioStream BGM;
AudioData MD;

try{
BGM = new AudioStream(new FileInputStream("boygeorge_2.au"));
MD = BGM.getData();
loop = new ContinuousAudioDataStream(MD);
}catch(IOException error){

}
MGP.start(loop);
}




void reset()
{  
   initializeValues();
   p1.removeAll();
   myt.stop();
   createBasket();
   music();
   t.setText("Score==>" + score);
   myt = new Thread(this);
   myt.start();
}

public void run() {
        for (;;) {
                    move();
                    
            try {cntr++;
                Thread.sleep(speed);
              
            } catch (InterruptedException e) {
            }
        }


}
}
public class MainGame12
{
  public static void main(String[] args) 
{
       new Fruitcatcher();      
    }  
}


class BackgroundPanel extends JPanel
{
Image img2;
public BackgroundPanel()
{
try
{
 Toolkit toolkit=Toolkit.getDefaultToolkit();
   img2 = toolkit.getImage("sunny.JPG");
}
catch(Exception e){}
}
public void paintComponent(Graphics g)
{
super.paintComponent(g);
 g.drawImage(img2,0,0,this);
}
}
