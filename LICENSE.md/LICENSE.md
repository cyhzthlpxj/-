/*
 * 文件: SicBo.java
 * ------------------------------
 * 骰宝游戏
 */

import java.awt.*;
import java.awt.event.*;
import java.util.*;
import javax.swing.*;
import acm.graphics.*;
import acm.io.IODialog;
import acm.program.*;
import acm.util.RandomGenerator;
public class SicBo extends GraphicsProgram implements SicBoConstants,ItemListener{
	//界面中交互组件的创建及添加、添加监听器
	public void init() {
		//设置背景颜色为绿色
		setBackground(new Color(0,220,0));
		
		//设置窗口大小
		setSize(APPLICATION_WIDTH, APPLICATION_HEIGHT);
		
		//设置原始赌资
		capital = initial_capital;
		
		//此处编写代码，实现初始化指定点数、初始化局数
        int sic = 0;
        int cnt = 0;
		//此处编写代码
		//实现创建新一局和开盅按钮，将它们添加到WEST，设置newBT不可使用，openBT可使用
		JButton but = new JButton("新一局");
		
		add(but ,WEST);
		
		JButton button = new JButton("开盅");
		
		add(button ,WEST);
		
		newBT.setEnabled(false);
		
		openBT.setEnabled(true);
	
		displaySicBobk();       //显示骰子背面

		initRadioButtons();    //添加单选按钮

		addActionListeners();     //添加命令按钮监听器
		
		//只对全围和对子这两个单选钮添加item监听器
		//因为只有这两种押注需要指定点数，当玩家押这两种之一时，要进行监听
		//需要弹出对话框，让玩家输入押注的点数
		weitouRB.addItemListener(this);
		duiziRB.addItemListener(this);
	}


	public void run() {
		
		GLabel namelab;
		
		IODialog dialog = getDialog();
		
		bunko = new int[500][2];
		
		playerName = dialog.readLine("请输入玩游戏人的姓名 ");
		
		namelab = new GLabel(playerName + " 欢迎你！！！" );
		
		namelab.setFont("Courier-20");
		
		add(namelab , 200, 30 );
		
		JOptionPane.showMessageDialog(null, "请您先选择下注种类，然后点击开盅，查看结果！！", "提示", JOptionPane.INFORMATION_MESSAGE);
		 
	}


	//点击命令按钮，激发actionPerformed事件，编写代码补全if和else if语句
	public void actionPerformed(ActionEvent e) {
		
		if (e.getActionCommand().equals("开盅")) { //玩家点击的是开盅按钮
			/*如果capital小于1注，也就是小于10，{弹出对话框提示：你余额不足！！游戏结束！！
			*设置newBT和openBT不可用}*/
			/*否则：{newBT设置为可用，openBT设置为不可用,调用spinSicBo（）产生随机数、
			 * displaySicBo()显示随机数相应的骰子、judgeBunko()判断输赢
			 * 和displayBunko()显示输赢信息}
			*/if(capital<10) {
				
				println("你的余额不足！！游戏结束！！");
				
				newBT.setEnabled(false);
				
				openBT.setEnabled(false);
				
			}
			
			else {
				newBT.setEnabled(true);
				
				openBT.setEnabled(false);
				
				spinSicBo();
				
				displaySicBo();
				
				judgeBunko();
				
				displayBunko();
		
			}
			
		}else if (e.getActionCommand().equals("新一局")) {//玩家点击的是新一局按钮
			//将指定点数清0，局数累加1
			//设置bigRB为选中状态
			//设置newBT设置为不可用，openBT设置为可用
			//调用方法显示骰子的背面
			sic = 0;
			
			cnt++;
			
			bigRB.isSelected();
			
			add(bigRB,WEST);
			
			newBT.setEnabled(false);
			
			openBT.setEnabled(true);
			
			displaySicBobk();  
			
		}

	}
	
	
	//单选钮选择状态发生改变时，会激发itemStateChanged事件
	public void itemStateChanged() {
		
		IODialog dialog = getDialog();

		if( weitouRB.isSelected() || duiziRB.isSelected()){
			
			while(true){
				
				sic = dialog.readInt("请输入押注的点数(1~6)");
				
				if(sic>=1 && sic<=6) break;
			}
		}
	}
	
	
	//判断玩家是否骰中，押大和押1点代码已给出，你完成其它15种的判断
	private void judgeBunko() {
		
		if(bigRB.isSelected()){ //玩家押的是大
			bunko[cnt][0] = 0;    //将押宝的类别赋值给该局的第0列，押大类别是0
			
			//三个点数和在11~17之间，玩家赢，但不能出现全围（出现全围玩家输）
			if ((sic1 +sic2 +sic3)>=11 && (sic1 +sic2 +sic3)<=17 
				&& !(sic1 == sic2 && sic1 == sic3)){
				bunko[cnt][1] = 1;   //if条件为真表示玩家赢，所以第1列赋值1
				capital += 10 * betFair[0];  //赚取0类别的赔率金额
			}else{
				bunko[cnt][1] = 0;   //if条件为假表示玩家输，所以赋值0
				capital -= 10;       //本金减去下注的金额10
			}
		}else if(smallRB.isSelected()){
			
			bunko[cnt][0] = 1;
			
			if((sic1+sic2+sic3)>=4 && (sic1+sic2+sic3)<=10 && !(sic1 == sic2 && sic1 == sic3)) {
				
				bunko[cnt][1] = 1;
				
				capital += 10 * betFair[1];  //赚取1类别的赔率金额
				
			}
			
			else {
				
				bunko[cnt][1] = 0;
				capital -= 10;
				
			}
			
		}else if(weitouRB.isSelected()){
			
			bunko[cnt][0] = 2;
			
			itemStateChanged();
			
			if(sic == sic1 && sic == sic2 && sic == sic3) {
				
				bunko[cnt][1] = 1;
				capital += 10 *  betFair[3];
				
			}
			
           else {
				
				bunko[cnt][1] = 0;
				capital -= 10;
				
			}
         
		}else if(quanweiRB.isSelected()){
			
			bunko[cnt][0] = 3;
			
			if(sic1 == sic2 && sic1 == sic3) {
				
				bunko[cnt][1] = 1;
				
				capital += 10 * 24;
				
			}
			
			else {
				
				bunko[cnt][1] = 0;
				capital -= 10;
				
			}
			
		}else if(s1RB.isSelected()){   //玩家押的单点，点数是1
			bunko[cnt][0] = 4;         //将押宝的类别赋值给该局的第0列，押1类别是4
			
			if (sic1 == 1 && sic1 == sic2 && sic1 == sic3){//如果出现三个1点，则赔率是3倍
				bunko[cnt][1] = 1;
				capital += 10 * 3;
			}else if ((sic1 == 1 && (sic1 == sic2 || sic1 == sic3))//如果出现二个1点，则赔率是2倍
					|| (sic2 == 1 && sic2 == sic3)  ){
				bunko[cnt][1] = 1;
				capital += 10 * 2;
			}else if (sic1 == 1 || sic2 == 1 || sic3 == 1 ){//如果出现一个1点，则赔率是4类的赔率
				bunko[cnt][1] = 1;
				capital += 10 * betFair[4];
			}else{
				bunko[cnt][1] = 0;   //if条件为假表示玩家输，所以赋值0
				capital -= 10;       //本金减去下注的金额10
			}
		}else if(s2RB.isSelected()){
			
			bunko[cnt][0] = 5;//玩家押的是单数2
			
			if(sic1== 2 && sic1 == sic2 && sic1 == sic3) {
				bunko[cnt][1] = 1;
				capital += 10 * 3;
			}else if((sic1 == 1 && (sic1 == sic2 || sic1 == sic3)) ||(sic2 == 1 && sic2 == sic3)  ) {
				bunko[cnt][1] = 1;
				capital += 10 * 2;
			}else if (sic1 == 1 || sic2 == 1 || sic3 == 1 ){//如果出现一个1点，则赔率是4类的赔率
				bunko[cnt][1] = 1;
				capital += 10 * betFair[4];
			}else{
				bunko[cnt][1] = 0;   //if条件为假表示玩家输，所以赋值0
				capital -= 10;       //本金减去下注的金额10
			}
			
		}else if(s3RB.isSelected()){
               bunko[cnt][0] = 6;         //将押宝的类别赋值给该局的第0列，押1类别是4
			
			if (sic1 == 3 && sic1 == sic2 && sic1 == sic3){//如果出现三个1点，则赔率是3倍
				bunko[cnt][1] = 1;
				capital += 10 * 3;
			}else if ((sic1 == 3 && (sic1 == sic2 || sic1 == sic3))//如果出现二个1点，则赔率是2倍
					|| (sic2 == 3 && sic2 == sic3)  ){
				bunko[cnt][1] = 1;
				capital += 10 * 2;
			}else if (sic1 == 3 || sic2 == 3 || sic3 == 3 ){//如果出现一个3点，则赔率是4类的赔率
				
				bunko[cnt][1] = 1;
				
				capital += 10 * betFair[4];
				
			}else{
				bunko[cnt][1] = 0;   //if条件为假表示玩家输，所以赋值0
				capital -= 10;       //本金减去下注的金额10
			}	
			
		}else if(s4RB.isSelected()){//玩家押的是 单数4
			
			 bunko[cnt][0] = 7;         //将押宝的类别赋值给该局的第0列，押1类别是4
				
				if (sic1 == 4 && sic1 == sic2 && sic1 == sic3){//如果出现三个1点，则赔率是3倍
					
					bunko[cnt][1] = 1;
					
					capital += 10 * 3;
					
				}else if ((sic1 == 4 && (sic1 == sic2 || sic1 == sic3))//如果出现二个1点，则赔率是2倍
						|| (sic2 == 4 && sic2 == sic3)  ){
					
					bunko[cnt][1] = 1;
					
					capital += 10 * 2;
					
				}else if (sic1 == 4|| sic2 == 4 || sic3 == 4 ){//如果出现一个4点，则赔率是4类的赔率
					
					bunko[cnt][1] = 1;
					
					capital += 10 * betFair[4];
					
				}else{
					
					bunko[cnt][1] = 0; //if条件为假表示玩家输，所以赋值0
					
					capital -= 10;       //本金减去下注的金额10
				}	
			
			
		}else if(s5RB.isSelected()){
			
			 bunko[cnt][0] = 8;         //将押宝的类别赋值给该局的第0列，押1类别是4
				
				if (sic1 == 5 && sic1 == sic2 && sic1 == sic3){//如果出现三个1点，则赔率是3倍
					
					bunko[cnt][1] = 1;
					
					capital += 10 * 3;
					
				}else if ((sic1 == 5 && (sic1 == sic2 || sic1 == sic3))//如果出现二个1点，则赔率是2倍
						|| (sic2 == 5 && sic2 == sic3)  ){
					
					bunko[cnt][1] = 1;
					
					capital += 10 * 2;
					
				}else if (sic1 == 5 || sic2 == 5 || sic3 == 5 ){//如果出现一个1点，则赔率是4类的赔率
					
					bunko[cnt][1] = 1;
					
					capital += 10 * betFair[4];
					
				}else{
					
					bunko[cnt][1] = 0; //if条件为假表示玩家输，所以赋值0
					
					capital -= 10;       //本金减去下注的金额10
				}	
			
			
		}else if(s6RB.isSelected()){
			
			 bunko[cnt][0] = 9;         //将押宝的类别赋值给该局的第0列，押6类别是9
				
				if (sic1 == 6 && sic1 == sic2 && sic1 == sic3){//如果出现三个1点，则赔率是3倍
					
					bunko[cnt][1] = 1;
					
					capital += 10 * 3;
					
				}else if ((sic1 == 6 && (sic1 == sic2 || sic1 == sic3))//如果出现二个1点，则赔率是2倍
						|| (sic2 == 6 && sic2 == sic3)  ){
					
					bunko[cnt][1] = 1;
					
					capital += 10 * 2;
					
				}else if (sic1 == 6 || sic2 == 6 || sic3 == 6 ){//如果出现一个6点，则赔率是4类的赔率
					
					bunko[cnt][1] = 1;
					
					capital += 10 * betFair[4];
					
				}else{
					bunko[cnt][1] = 0; //if条件为假表示玩家输，所以赋值0
					
					capital -= 10;       //本金减去下注的金额10
				}	
			
			
		}else if(duiziRB.isSelected()){
			
            bunko[cnt][0] = 10;
			
			itemStateChanged();
			
			if((sic == sic1 && (sic1 == sic2 || sic1 == sic3))||(sic == sic2 && sic2 == sic3)  || (sic == sic1 && sic == sic1 && sic == sic3)){
				
				bunko[cnt][1] = 1;
				
				capital+=10 * 8;
			}
			
			
           else {
				
				bunko[cnt][1] = 0;
				capital -= 10;
				
			} 
			
		}else if(d4_17RB.isSelected()){
			
			bunko[cnt][0] = 11;
			
			if((sic1 +sic2+sic3)==17 || (sic1 + sic2 + sic3)==4) {
				
				bunko[cnt][1] = 1;
				
				capital += 10 *  betFair[11];
			}
			
			else {
				
				bunko[cnt][1] = 0;
				
				capital -= 10;
			}
			
			
		}else if(d5_16RB.isSelected()){
			
			bunko[cnt][0] = 12;
			
			if((sic1+sic2+sic3)==5 || (sic1+sic2+sic3)==16) {
				
				bunko[cnt][1] = 1;
				
				capital += 10 *  betFair[12];
			}
			
			else {
				
				bunko[cnt][1] = 0;
				
				capital -= 10;
			}
			
			
			
		}else if(d6_15RB.isSelected()){
			
			bunko[cnt][0] = 13;
			
			if((sic1+sic2+sic3)==6 || (sic1+sic2+sic3)==15) {
				
				bunko[cnt][1] = 1;
				
				capital += 10 *  betFair[13];
			}
			else {
				bunko[cnt][1] = 0;
				capital -= 10;
			}
		}else if(d7_14RB.isSelected()){
			
			bunko[cnt][0] = 14;
			
			if((sic1+sic2+sic3)==7 || (sic1+sic2+sic3)==14) {
				
				bunko[cnt][1] = 1;
				
				capital += 10 *  betFair[14];
			}
			else {
				
				bunko[cnt][1] = 0;
				
				capital -= 10;
			}
			
		}else if(d8_13RB.isSelected()){
			
			bunko[cnt][0] = 15;
			
			if((sic1+sic2+sic3)==8 || (sic1+sic2+sic3)==13) {
				
				bunko[cnt][1] = 1;
				
				capital += 10 *  betFair[15];
			}
			
			else {
				bunko[cnt][1] = 0;
				capital -= 10;
			}
			
			
		}else if(d9_12RB.isSelected()){
			bunko[cnt][0] = 16;
			if((sic1+sic2+sic3)==9 || (sic1+sic2+sic3)==10 ||(sic1+sic2+sic3 )== 11||(sic1+sic2+sic3) == 12) {
				
				bunko[cnt][1] = 1;
				
				capital += 10 *  betFair[16];
			}
			
			else {
				bunko[cnt][1] = 0;
				capital -= 10;
			}
			
		}
	}
	
	
	//显示玩家输赢及本金情况
	private void displayBunko() {
		double x = 30 , y = 300;
	    int type = bunko[cnt][0];
	    
		if (bunko[cnt][1] == 1){
			GLabel lab = new GLabel("第" + Integer.toString(cnt+1)+"局: 您押的是" +
					betType[type] + "。恭喜，您押中了！！您的本金为"+Integer.toString(capital));
			
			lab.setFont("Courier-16");

			double dy = lab.getHeight();

			for(int i = 0; i < labels.size(); i++){
				labels.get(i).move(0, dy);
			}
           
			add(lab , x , y);
			labels.add(lab);
		}else{
			GLabel lab = new GLabel("第" + Integer.toString(cnt+1)+"局: 您押的是" +
					betType[type] + "。很遗憾，您未押中！！您的本金为"+Integer.toString(capital));
			lab.setFont("Courier-16");

			double dy = lab.getHeight();
			
			for(int i = 0; i <labels.size(); i++){
				labels.get(i).move(0, dy);
			}

			add(lab , x , y);
			labels.add(lab);
		}		
	
}


	//编写代码，产生三个骰子的三个随机点数(1~6)，并赋值给sic1,sic2,sic3
	private void spinSicBo() {
		
		 RandomGenerator regn = new RandomGenerator();
		 sic1 = regn.nextInt(1,6);
		 sic2 = regn.nextInt(1,6);
		 sic3 = regn.nextInt(1,6);
		
	}


	//显示盅未打开的图片
	private void displaySicBobk() {
		
		sicImage1 = new GImage("");
		
		sicImage1.setImage("bk.png");
		
		sicImage1.scale(1.0);
		
		add(sicImage1, 30 , 50);

		sicImage2 = new GImage("");
		sicImage2.setImage("bk.png");
		sicImage2.scale(1.0);
		add(sicImage2, 170 , 50);

		sicImage3 = new GImage("");
		sicImage3.setImage("bk.png");
		sicImage3.scale(1.0);
		add(sicImage3, 310 , 50);
	}


	//显示骰子点数图片
	private void displaySicBo() {
		sicImage1.setImage(Integer.toString(sic1) + ".png");
		sicImage1.scale(2.0);
		add(sicImage1, 30 , 50);

		sicImage2.setImage(Integer.toString(sic2) + ".png");
		sicImage2.scale(2.0);
		add(sicImage2, 170 , 50);

		sicImage3.setImage(Integer.toString(sic3) + ".png" );
		sicImage3.scale(2.0);
		add(sicImage3, 310 , 50);
	}


	//添加下注种类单选钮
	private void initRadioButtons() {
		
		ButtonGroup sizeBG = new ButtonGroup();

		//编写代码，创建bigRB~d9_12RB按钮，并分别设置单选钮上的文字
		bigRB = new JRadioButton("大");
		smallRB = new JRadioButton("小");
		weitouRB = new JRadioButton("围骰");   //围骰
	    quanweiRB = new JRadioButton("全围") ;  //全围
		s1RB = new JRadioButton("押1");       //押1
		s2RB = new JRadioButton("押2");       //押2
		s3RB = new JRadioButton("押3");       //押3
		s4RB =new JRadioButton("押4") ;       //押4
		s5RB = new JRadioButton("押4");       //押5
		s6RB = new JRadioButton("押6");       //押6
		duiziRB = new JRadioButton("押对子");    //押对子
		d4_17RB = new JRadioButton("押4或17");    //押总数为4或17
		d5_16RB = new JRadioButton("押5或16");    //押总数为5或16
		d6_15RB = new JRadioButton("押6或15");    //押总数为6或15
		d7_14RB = new JRadioButton("押7或14");    //押总数为7或14
		d8_13RB = new JRadioButton("押8或13");    //押总数为8或13
		d9_12RB = new JRadioButton("押9，10，11或12");//押总数为9，10，11或12 
		

		//编写代码，将单选钮添加到sizeBG按钮组中
		sizeBG.add(bigRB);
		sizeBG.add(smallRB);
		sizeBG.add(weitouRB);
		sizeBG.add(quanweiRB);
		sizeBG.add(s1RB);
		sizeBG.add(s2RB);
		sizeBG.add(s3RB);
		sizeBG.add(s4RB);
		sizeBG.add(s5RB);
		sizeBG.add(s6RB);
		sizeBG.add(duiziRB);
		sizeBG.add(d4_17RB);
		sizeBG.add(d5_16RB);
		sizeBG.add(d6_15RB);
		sizeBG.add(d7_14RB);
		sizeBG.add(d8_13RB);
		sizeBG.add(d9_12RB);
		

		//编写代码，设置bigRB为默认选中
		bigRB.setSelected(true);
		//将单选按钮添加到窗口的WEST区
		add(new JLabel("基本下注类型"), WEST);
		//编写代码，将bigRB、smallRB、weitouRB和quanweiRB添加到WEST
		add(bigRB,WEST);
		add(smallRB,WEST);
		add(weitouRB,WEST);
		add(quanweiRB,WEST);
		add(new JLabel("对单一点数下注"), WEST);
		//编写代码，将s1RB~s6RB和duiziRB添加到WEST
		add(s1RB,WEST);
		add(s2RB,WEST);
		add(s3RB,WEST);
		add(s4RB,WEST);
		add(s5RB,WEST);
		add(s6RB,WEST);
		add(duiziRB,WEST);
		
		add(new JLabel("对总数下注"), WEST);
		//编写代码，将d4_17RB、d5_16RB、d6_15RB、d7_14RB、d8_13RB和d9_12RB添加到WEST
		add(d4_17RB,WEST);
		add(d5_16RB,WEST);
		add(d6_15RB,WEST);
		add(d7_14RB,WEST);
		add(d8_13RB,WEST);
		add(d9_12RB,WEST);
	}


	
	/* 实例变量*/
	private JButton newBT;           //新一局按钮
	private JButton openBT;          //开盅按钮
	
	private JRadioButton bigRB;      //大
	private JRadioButton smallRB;    //小
	private JRadioButton weitouRB;   //围骰
	private JRadioButton quanweiRB;  //全围
	private JRadioButton s1RB;       //押1
	private JRadioButton s2RB;       //押2
	private JRadioButton s3RB;       //押3
	private JRadioButton s4RB;       //押4
	private JRadioButton s5RB;       //押5
	private JRadioButton s6RB;       //押6
	private JRadioButton duiziRB;    //押对子
	private JRadioButton d4_17RB;    //押总数为4或17
	private JRadioButton d5_16RB;    //押总数为5或16
	private JRadioButton d6_15RB;    //押总数为6或15
	private JRadioButton d7_14RB;    //押总数为7或14
	private JRadioButton d8_13RB;    //押总数为8或13
	private JRadioButton d9_12RB;    //押总数为9、10、11或12

	//bunko（输赢）第一列表示下注类型，参看SicBoConstants中下注类别号
	//bunko第二列存储玩家输赢情况，押中的话为1，未押中为0
	private int[][] bunko;
	
	//赌资
	private int capital;
	
	//玩家姓名
	private String playerName;
	
	//玩家押注时，指定的点数
	private int sic;
	
	//局数
	private int cnt;
	
	//存储三个骰子的点数
	private int sic1, sic2, sic3;
	
	//存储三个骰子的点数的图片
	GImage sicImage1 ,sicImage2, sicImage3;
	
	//在displayBunko()方法中，将输出的信息添加到动态数组labels中
	ArrayList <GLabel> labels = new ArrayList<GLabel>();

	@Override
	public void itemStateChanged(ItemEvent arg0) {
		// TODO 自动生成的方法存根
		
	}
	
	//private RandomGenerator rgen = new RandomGenerator();


}




