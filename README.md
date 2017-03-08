# mine-sweeping
一款经典的小游戏，有一点小错误，希望多多修改
package com.view;

import java.awt.BorderLayout;
import java.awt.Color;
import java.awt.FlowLayout;
import java.awt.GridLayout;
import java.awt.Label;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;

public class Mine extends MouseAdapter {
	private JFrame mainFrame;//窗口对象
	private int[][] data;//数据二维数组
	private JButton startJB;//开始按钮
	private JButton[][] buttons;//按钮二维数组
	private Label l;//标签  分数
	private int row;//行
	private int col;//列
	private int mineNumber;//雷数
	private int mineCount;//被找到的雷数
	private boolean isOver;//游戏是否结束
	
	public Mine(){//构造方法
		row=15;
		col=15;
		mainFrame=new JFrame("扫雷");//创建窗口对象
		data =new int [row][col];//定义数组
		buttons=new JButton[row][col];
		startJB=new JButton("start");
		l=new Label("welcome to main!");
		mineNumber=row*col/7;
	}
	
	public void init(){              //初始化方法
		JPanel north=new JPanel();
		JPanel south= new JPanel();
		JPanel center = new JPanel();
		north.setLayout(new FlowLayout());//布局
		center.setLayout(new FlowLayout());
		south.setLayout(new GridLayout(row,col,4,4));//雷区采用Grid布局
		mainFrame.setLayout(new BorderLayout());
		mainFrame.add(north,BorderLayout.NORTH);//分别添加面板
		mainFrame.add(center,BorderLayout.CENTER);
		mainFrame.add(south,BorderLayout.SOUTH);
		
		north.add(l);//添加标签
		startJB.addActionListener(new ActionListener(){//开始按钮监听器
			public void actionPerformed(ActionEvent e){//一起初始化开始
				for(int i=0;i<row;i++){
					for(int j=0;j<col;j++){
						buttons[i][j].setText("") ;
						buttons[i][j].setBackground(Color.WHITE);
						data[i][j]=0;
						isOver=false;
					}
				}
				hashMine();
				mineCount=0;
				l.setText("let's go!");
			}
		});
		center.add(startJB);
	for(int i=0;i<row;i++){//初始化按钮信息
		for(int j=0;j<col;j++){
		buttons[i][j]=new JButton("");
		buttons[i][j].setName((i+":"+j));//按钮的名字记录着行和列
		buttons[i][j].setBackground(Color.WHITE);
		buttons[i][j].addMouseListener(this);
		south.add(buttons[i][j]);
	}
	}
	hashMine();
}
public void start(){
	
	mainFrame.setSize(800,600);
	mainFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	mainFrame.setVisible(true);
}
	
	
	private void hashMine() {  //随机布雷方法
		//得到随机数，确定雷的位置
		for(int i=0;i<mineNumber;i++){
			data[(int) (Math.random()*row)][(int) (Math.random()*col)]=-1;
		}
		for(int i=0;i<row;i++){
			for(int j=0;j<col;j++){
				if(data[i][j]==-1)
					continue;
				//确定每一个格子周围的雷的数量
				int sum=0;
				for(int m=-1;m<=1;m++){
					for(int n=-1;n<=1;n++){
						if(i+m>=0&&j+n>=0&&i+m<row&&j+n<col){
							if(data[i+m][j+n]==-1)
								sum++;
						}
					}
				}
				data[i][j]=sum;
			}
		}
	}
	private void gameOver(boolean over){//游戏结束
		if(over==true){
			for(int i=0;i<row;i++){//把所有的雷显示出来
				for (int j=0;j<col;j++){
					if(data[i][j]==-1);{
						buttons[i][j].setText("M");
						buttons[i][j].setBackground(Color.RED);
					}
					
				}
			}
		l.setText("-_-");
		isOver=true;
		return;
	}
		int sumPress=0;//统计被着色的按钮总数
		for(int i=0;i<row;i++){
			for(int j=0;j<col;j++){
				if(!buttons[i][j].getText().equals("")){
					sumPress++;
				}
			}
		}
		if(sumPress==row*col){
			int sum=0;
			for(int i=0;i<row;i++){
				for (int j=0;j<col;j++){
					if(data[i][j]==-1&&buttons[i][j].getText().equals("M")){
						sum++;
					}
				}
			}
			if(sum>=mineNumber){
				System.out.print(mineNumber);
				l.setText("^_^");
			}
		}
	}
	public void mousePressed(MouseEvent e){//监听鼠标点击事件
		try{
			if(isOver)
				return;
			if(e.getButton()==MouseEvent.BUTTON3){//点击右键
				JButton jb=(JButton)e.getSource();//活的被点的按钮对象
				if(jb.getText().equals("M")){//取消标志
					jb.setText("");
					mineCount--;
					jb.setBackground(Color.WHITE);
				}else{
					if(mineCount<mineNumber){//标旗还没有用完
						jb.setText("M");
						jb.setBackground(Color.BLUE);
						mineCount++;
					}else{//标旗已经用完
						l.setText("THE MINE FLAG IS OVER!");
						
					}
				}
			}else{
				JButton jb=(JButton)e.getSource();
				mousePress(jb);//调用mousePress（）方法处理鼠标左键被点击的事件
			}
		}catch(Exception ex){
			ex.printStackTrace();
		}
		gameOver(false);//主要用于判断游戏是否结束，雷是否被找完
	}
				private void mousePress(JButton jb){
					String str[]=jb.getName().split(":");//得到行号列号
					int i=Integer.parseInt(str[0]);
					int j=Integer.parseInt(str[1]);
					if(data[i][j]==-1){//如果点到雷
						gameOver(true);
						return;
					}else{
						jb.setText(data[i][j]+"");//设置文本和颜色
						jb.setBackground(Color.YELLOW);
						if(data[i][j]==0){//如果四周都没有雷
							for(int m=-1;m<=1;m++){
								for(int n=-1;n<=1;n++){
									if(i+m>=0&&j+n>=0&&i+m<row&&j+n<col){
										if(buttons[i+m][j+n].getText().equals(""))
											mousePress(buttons[i+m][j+n]);//撬开傍边按钮
									}
								}
							}
						}
					}
				}
				public static void main(String[] args){//主方法
					Mine mine=new Mine();
				mine.init();
				mine.start();
			}
		}
	
	

	



