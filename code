```c++
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<math.h>
#include<windows.h>
#include<conio.h>


FILE *fp;//文件指针 

int num_products,//物品总数
day=1,//日期
choice,//控制菜单选择
index;//循环变量
//day_flag;//判断是否已从data.dat文件中读取过day变量的值
 
char c,//读取调用_getch()时键盘输入的字符
kaishi[2][20]={"* YesYesYes!  *","* NoNoNo!     *"},
kaishi_ch[2][20]={"* YesYesYes!< *","* NoNoNo!<    *"}, //选择yes or no 
change_menu[2][20]={"* 修改信息  *","* 取消密码  *"},
change_menu_ch[2][20]={"* 修改信息< *","* 取消密码< *"};//选择“修改信息”和“取消密码” 

//结构体的定义，用于储存物品信息
typedef struct item
{
	char name[20];//物品名字 
	int quantity;//物品数量
	int cost;//物品成本 
	int price;//物品价格
	int record[31];//物品数量增减记录 
	int password_setted;//用于判断该物品是否设置了密码 
	char password[18];//密码 
}item;

item itemArray[100];//结构体数组，储存物品信息


void menu();//声明菜单函数，方便在功能函数里调用实现返回菜单 



//梯度下降求解回归方程，用于给出未来物品数量的预测，样本越多预测越准确
double w,//斜率
b,//常数项，截距
tmp_w,tmp_b,//临时变量，用于实现w，b的同步更新
alpha=1.0;//学习率 


//用线性回归模型来拟合 
double record_hat(int future)
{
	return w*day+b;//线性回归方程 
}


//损失函数的偏导数 
double j1(int day,int index)
{
	double addition=(record_hat(day)-itemArray[index].record[day])*day;
	if (day==1) return addition;
	return ((day-1)*j1(day-1,index)+addition)/day;
}


//用递归实现梯度下降 
double gradient_descent(int day,int future)
{
	tmp_w=w-alpha*j1(day,index);
	tmp_b=b-alpha*j1(day,index);
	w=tmp_w;
	b=tmp_b;
	if (abs(tmp_w-w)>=0.2) gradient_descent(day,future);//递归终止条件 
	return record_hat(future);
}




//文件读写 

void read()//将文件信息读入结构体数组 
{
	FILE *fp;
	num_products=0;
	fp=fopen("D:/data.dat","wb+");
	for (num_products=0;!feof(fp);num_products++)
	fread(&itemArray[num_products],sizeof(item),1,fp);
	fclose(fp);
}

void write()//将结构体数组写入文件 
{
	FILE *fp;
	fp = fopen("data.dat", "wb+");
	fwrite(itemArray,sizeof(item),num_products,fp);
	fclose(fp);
}











//功能函数

//int flag=0;//判断是否已读取过文件 
//录入仓库物品信息
void insert()
{
	system("cls");
	if (num_products==100)//仓库已满 
	{
		while (1)
		{
			system("cls");
			printf("仓库已满，录入失败!\n\n是否返回菜单？\n\n");//是否返回菜单 
			printf("===============\n");
			printf("* YesYesYes!< *\n");
			printf("===============\n"); 
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='\r')
			{
				system("cls");
				printf("正在返回菜单…");
				Sleep(1500);
				menu();
			}
		}
	}
	else
	{
		char name1[20];
		printf("请输入物品名字：");//输入物品名字查询 
		scanf("%s",name1);
		for (int q=0;q<num_products;q++)
		if (strcmp(itemArray[q].name,name1)==0)//物品已存在 
		{
			choice=0;
			while (1)
			{
				int i=0;
				system("cls");
				printf("该物品已存在，是否修改？\n\n");//是否修改 
				printf("===============\n");
				for(;i<2;i++)
				{
					if (choice==i) printf("%s\n",kaishi_ch[i]);
					else printf("%s\n",kaishi[i]);
				}
				printf("===============\n"); 
				printf("按w/s选择，按回车确认\n");
				c= _getch();
				if (c=='w') choice--;
				if (c=='s') choice++;
				if (choice>1) i=choice=0;
				if (choice<0) i=choice=1;
				if (c=='\r')
				switch(choice)
				{
					case 0://修改 
					{
						choice=0;
						if (itemArray[q].password_setted)//该物品被设置过密码 
						{
							while (1)
							{
								int i=0;
								system("cls");
								printf("===============\n");
								for(;i<2;i++)
								{
									if (choice==i) printf("%s\n",change_menu_ch[i]);//选择“修改信息”or“取消密码” 
									else printf("%s\n",change_menu[i]);
								}
								printf("===============\n"); 
								printf("按w/s选择，按回车确认\n");
								c= _getch();
								if (c=='w') choice--;
								if (c=='s') choice++;
								if (choice>1) i=choice=0;
								if (choice<0) i=choice=1;
								if (c=='\r')
								switch(choice)
								{
									case 0://选择“修改信息” 
									{
										while (1)
										{
											system("cls");
											printf("请输入密码：");
											int i = 0; //记录密码长度
											char password_input[18];//用于储存密码 
											char c; //用于实现密码隐式输入
											while (1)
											{
												c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
											    if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
												break; //while 循环的出口
											    else if (c == '\b') 
												{ //遇到退格，需要删除前一个星号
												    printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
												    --i;
												}
											    else
												{
													if (i>17||i<5&&c=='\r')//密码长度小于6或大于18 
													{
														printf("\n\n密码须为6-18位！");
														Sleep(1500);
														system("cls");
														printf("请输入密码：");
														for (int j=0;j<i;j++) printf("*");
													}
											        else
													{
														password_input[i++] = c;//将字符放入数组
												       	printf("*");
												    }
												}
											}
											if (strcmp(password_input,itemArray[q].password)==0)//密码正确
											{
												system("cls");
												printf(" %s 的原信息：\n\n",itemArray[q].name);//输出原物品的信息
												int tmp=itemArray[q].quantity;
												printf("数量：%d\n",itemArray[q].quantity);
												printf("成本：%d\n",itemArray[q].cost);
												printf("价格：%d\n\n",itemArray[q].price);
												printf("已完成对物品的原信息的输出\n\n");
												printf("请进行修改（若物品信息中有不想修改的请输入原信息）\n\n"); //修改物品的各类信息 
												printf("名称： ");
												scanf("%s",itemArray[q].name);
												printf("\n数量：");
												scanf("%d",&itemArray[q].quantity);
												itemArray[q].record[day]+=itemArray[q].quantity-tmp;
												if (itemArray[q].quantity==0)
												{
													for (int i = num_products - 2; i >= q; i--) itemArray[i]=itemArray[i + 1]; // 将后面的物品移到前面来填补空位
						        					num_products--; // 更新仓库中现有物品数量
												}
												if (itemArray[q].quantity)//数量不为0时的修改 
												{
													printf("\n成本：");
													scanf("%d",&itemArray[q].cost);
													printf("\n价格：");
													scanf("%d",&itemArray[q].price);
													printf("\n密码：");
													int i=0,lower=0,upper=0,digit=0; //记录密码长度 
													char c; //用于实现密码隐式输入
													while (1)
													{
														c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
														lower+=islower(c); 
														upper+=isupper(c);
														digit+=isdigit(c);//通过密码中大小写字母以及数字是否出现过判断密码强弱 
														if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
														break; //while 循环的出口
														else if (c == '\b') 
														{ //遇到退格，需要删除前一个星号
															printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
															--i;
														}
														else
														{
															if (i>17||i<5&&c=='\r')// 密码长度小于6或大于18 
															{
																printf("\n\n密码须为6-18位！");
																Sleep(1500);
																system("cls");
																printf("密码：");
																for (int j=0;j<i;j++) printf("*");
															}
															else
															{
																itemArray[q].password[i++] = c;//将字符放入数组
															    printf("*");
														    }
													    }
													}
													if (lower&&upper&&digit) printf("\n\n强密码！");//密码中同时设置大小写字母与数字，判定为强密码 
													else printf("\n\n弱密码！");
												}
												else printf("\n删除成功！");//数量为0时的删除 
												Sleep(1500);
												system("cls");
												printf("修改完成！");//修改完成
												Sleep(1500);
												choice=0;
												while (1)
												{
													int i=0;
													system("cls");
													printf("是否继续录入？\n\n");
													printf("===============\n");
													for(;i<2;i++)
													{
														if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否继续录入 
														else printf("%s\n",kaishi[i]);
													}
													printf("===============\n"); 
													printf("按w/s选择，按回车确认\n");
													c= _getch();
													if (c=='w') choice--;
													if (c=='s') choice++;
													if (choice>1) i=choice=0;
													if (choice<0) i=choice=1;
													if (c=='\r')
													switch(choice)
													{
														case 0: insert();//继续录入 
														case 1://返回菜单 
														{
															system("cls");
															printf("正在返回菜单…");
															Sleep(1500);
															menu();
														}
													}
												}
											}
											else//密码错误 
											{
												printf("\n\n密码错误，请重新输入！");
												Sleep(1500);
												system("cls");
												printf("请输入密码："); 
												for (int j=0;j<i;j++) printf("*");
											}
										} 
									}
									case 1://选择取消密码 
									{
										while (1)
										{
											system("cls");
											printf("请输入密码：");
											int i = 0; //记录密码长度
											char password_input[18];//用于储存密码 
											char c; //用于实现密码隐式输入
											while (1)
											{
												c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
											    if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
												break; //while 循环的出口
											    else if (c == '\b') 
												{ //遇到退格，需要删除前一个星号
												    printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
												    --i;
												}
											    else
												{
													if (i>17||i<5&&c=='\r')//密码长度小于6或大于18 
													{
														printf("\n\n密码须为6-18位！");
														Sleep(1500);
														system("cls");
														printf("请输入密码：");
														for (int j=0;j<i;j++) printf("*");
													}
											        else
													{
														password_input[i++] = c;//将字符放入数组
												       	printf("*");
												    }
												}
											}
											if (strcmp(password_input,itemArray[q].password)==0)//密码正确
											{
												itemArray[q].password_setted=0;//将该物品判定为未设置密码 
												system("cls");
												printf("密码已取消！");
												Sleep(1500);
												choice=0;
												while (1)
												{
													int i=0;
													system("cls");
													printf("是否继续录入？\n\n");
													printf("===============\n");
													for(;i<2;i++)
													{
														if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否继续录入 
														else printf("%s\n",kaishi[i]);
													}
													printf("===============\n"); 
													printf("按w/s选择，按回车确认\n");
													c= _getch();
													if (c=='w') choice--;
													if (c=='s') choice++;
													if (choice>1) i=choice=0;
													if (choice<0) i=choice=1;
													if (c=='\r')
													switch(choice)
													{
														case 0: insert();//继续录入 
														case 1://返回菜单 
														{
															system("cls");
															printf("正在返回菜单…");
															Sleep(1500);
															menu();
														}
													}
												}
											}
											else//密码错误 
											{
												printf("\n\n密码错误，请重新输入！");
												Sleep(1500);
												system("cls");
												printf("请输入密码：");
												for (int j=0;j<i;j++) printf("*");
											}
										}
									}
								}
							}
						}
						else//未设置密码 
						{
							system("cls");
							printf(" %s 的原信息：\n\n",itemArray[q].name);//输出原物品的信息
							int tmp=itemArray[q].quantity;
							printf("数量：%d\n",itemArray[q].quantity);
							printf("成本：%d\n",itemArray[q].cost);
							printf("价格：%d\n",itemArray[q].price);
							printf("已完成对物品的原信息的输出\n\n");
							printf("请进行修改（若物品信息中有不想修改的请输入原信息）\n\n"); //修改物品的各类信息 
							printf("名称：");
							scanf("%s",itemArray[q].name);
							printf("\n数量：");
							scanf("%d",&itemArray[q].quantity);
							itemArray[q].record[day]+=itemArray[q].quantity-tmp;
							if (itemArray[q].quantity==0)
							{
								for (int i = num_products - 2; i >= q; i--) itemArray[i]=itemArray[i + 1]; // 将后面的物品移到前面来填补空位
			        			num_products--; // 更新仓库中现有物品数量
							}
							if (itemArray[q].quantity)//数量不为0时的修改 
							{
								printf("\n成本：");
								scanf("%d",&itemArray[q].cost);
								printf("\n价格：");
								scanf("%d",&itemArray[q].price);
								choice=0;
								while (1)
								{
									int i=0;
									system("cls");
									printf("是否设置密码？\n\n");
									printf("===============\n");
									for(;i<2;i++)
									{
										if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否设置密码 
										else printf("%s\n",kaishi[i]);
									}
									printf("===============\n"); 
									printf("按w/s选择，按回车确认\n");
									c= _getch();
									if (c=='w') choice--;
									if (c=='s') choice++;
									if (choice>1) i=choice=0;
									if (choice<0) i=choice=1;
									if (c=='\r')
									switch(choice)
									{
										case 0://设置密码
										{
											system("cls");
											itemArray[q].password_setted=1;
											printf("请输入密码：");
											int i=0,lower=0,digit=0,upper=0; //记录密码长度
											char c; //用于实现密码隐式输入
											while (1)
											{
										    	c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
											    lower+=islower(c);
												upper+=isupper(c);
												digit+=isdigit(c);//通过密码中是否同时含有大小写字母及数字来判定密码强弱 
											    if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
												break; //while 循环的出口
											    else if (c == '\b') 
												{ //遇到退格，需要删除前一个星号
											        printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
											        --i;
											    }
											    else
												{
													if (i>17||i<5&&c=='\r')
													{
														printf("\n\n密码须为6-18位！");
														Sleep(1500);
														system("cls");
														printf("请输入密码：");
														for (int j=0;j<i;j++) printf("*");
													}
											        else
													{
														itemArray[q].password[i++] = c;//将字符放入数组
											        	printf("*");
											        }
											    }
											}
											if (lower&&upper&&digit) printf("\n\n强密码！");//密码中同时设置了大小写字母与数字，判定为强密码 
											else printf("\n\n弱密码！");
											Sleep(1500);
											system("cls");
											printf("修改完成！");//修改完成
											Sleep(1500);
											choice=0;
											while (1)
											{
												int i=0;
												system("cls");
												printf("是否继续录入？\n\n");
												printf("===============\n");
												for(;i<2;i++)
												{
													if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续录入 
													else printf("%s\n",kaishi[i]);
												}
												printf("===============\n"); 
												printf("按w/s选择，按回车确认\n");
												c= _getch();
												if (c=='w') choice--;
												if (c=='s') choice++;
												if (choice>1) i=choice=0;
												if (choice<0) i=choice=1;
												if (c=='\r')
												switch(choice)
												{
													case 0:insert();//继续录入 
													case 1://返回菜单 
													{
														system("cls");
														printf("正在返回菜单…");
														Sleep(1500);
														menu();
													}
												}
											}
									    }
										case 1://不设置密码 
										{
											system("cls");
											printf("修改完成！");//修改完成
											Sleep(1500);
											choice=0;
											while (1)
											{
												int i=0;
												system("cls");
												printf("是否继续录入？\n\n");
												printf("===============\n");
												for(;i<2;i++)
												{ 
													if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否继续录入 
													else printf("%s\n",kaishi[i]); 
												}
												printf("===============\n"); 
												printf("按w/s选择，按回车确认\n");
												c= _getch();
												if (c=='w') choice--;
												if (c=='s') choice++;
												if (choice>1) i=choice=0;
												if (choice<0) i=choice=1;
												if (c=='\r')
												switch(choice)
												{
													case 0:insert();//继续录入 
													case 1://返回菜单 
													{
														system("cls");
														printf("正在返回菜单…");
														Sleep(1500);
														menu();
													}
												}
											}
										}
									}
								}
							}
							else//数量为0时的删除 
							{
								printf("\n删除成功！");
								Sleep(1500);
								system("cls");
								printf("修改完成");//修改完成
								Sleep(1500);
								choice=0;
								while (1)
								{
									int i=0;
									system("cls");
									printf("是否继续录入？\n\n");
									printf("===============\n");
									for(;i<2;i++)
									{
										if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否继续录入 
										else printf("%s\n",kaishi[i]);
									}
									printf("===============\n"); 
									printf("按w/s选择，按回车确认\n");
									c= _getch();
									if (c=='w') choice--;
									if (c=='s') choice++;
									if (choice>1) i=choice=0;
									if (choice<0) i=choice=1;
									if (c=='\r')
									switch(choice)
									{
										case 0:insert();//继续录入 
										case 1://返回菜单 
										{
											system("cls");
											printf("正在返回菜单…");
											Sleep(1500);
											menu();
										}
									}
								}
							}
						}
					}
					case 1://放弃修改 
					{
						system("cls");
						choice=0;
						while (1)
						{
							int i=0;
							system("cls");
							printf("是否继续录入？\n\n");
							printf("===============\n");
							for(;i<2;i++)
							{
								if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否继续录入 
								else printf("%s\n",kaishi[i]);
							}
							printf("===============\n"); 
							printf("按w/s选择，按回车确认\n");
							c= _getch();
							if (c=='w') choice--;
							if (c=='s') choice++;
							if (choice>1) i=choice=0;
							if (choice<0) i=choice=1;
							if (c=='\r')
							switch(choice)
							{
								case 0: insert();//继续录入 
								case 1://返回菜单 
								{
									system("cls");
									printf("正在返回菜单…");
									Sleep(1500);
									menu();
								}
							}
						} 
					}
				}
			}
		}
		//该物品不存在，执行录入操作 
		//从键盘键入数据
		strcpy(itemArray[num_products].name,name1);
		system("cls");
		printf("请输入物品数量："); 
		scanf("%d",&itemArray[num_products].quantity);
		itemArray[num_products].record[day]=itemArray[num_products].quantity;
		system("cls");
		itemArray[num_products].record[day]=itemArray[num_products].quantity;
		printf("请输入物品成本：");
		scanf("%d",&itemArray[num_products].cost);
		system("cls");
		printf("请输入物品价格："); 
		scanf("%d",&itemArray[num_products].price);
		system("cls");
		choice=0;
		while (1)
		{
			int i=0;
			system("cls");
			printf("是否设置密码？\n\n");
			printf("===============\n");
			for(;i<2;i++)
			{
				if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否设置密码 
				else printf("%s\n",kaishi[i]);
			}
			printf("===============\n"); 
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='w') choice--;
			if (c=='s') choice++;
			if (choice>1) i=choice=0;
			if (choice<0) i=choice=1;
			if (c=='\r')
			switch(choice)
			{
				case 0://设置密码
				{
					system("cls");
					itemArray[num_products].password_setted=1;//将该物品判定为“已设置密码” 
					printf("请输入密码：");
					int i=0,lower=0,digit=0,upper=0; //记录密码长度
					char c; //用于实现密码隐式输入
					while (1)
					{
						c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
						lower+=islower(c);
						upper+=isupper(c);
						digit+=isdigit(c);
						if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
						break; //while 循环的出口
						else if (c == '\b') 
						{ //遇到退格，需要删除前一个星号
							printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
						    --i;
						}
						else
						{
							if (i>17||i<5&&c=='\r')//密码位数小于6大于18 
							{
								printf("\n\n密码须为6-18位！");
								Sleep(1500);
								system("cls");
								printf("请输入密码：");
								for (int j=0;j<i;j++) printf("*");
							}
					        else
							{
								itemArray[num_products].password[i++] = c;//将字符放入数组
					        	printf("*");
					        }
					    }
					}
					if (lower&&upper&&digit) printf("\n\n强密码！");//密码中同时设置大小写字母以及数字，判定为强密码 
					else printf("\n\n弱密码！");
					Sleep(1500);
					system("cls");
					num_products++;//更新仓库物品数量 
					choice=0;
					while (1)
					{
						system("cls");
						int i=0;
						printf("是否继续录入？\n\n");
						printf("===============\n");
						for(;i<2;i++)
						{
							if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否继续录入 
							else printf("%s\n",kaishi[i]);
						}
						printf("===============\n"); 
						printf("按w/s选择，按回车确认\n");
						c= _getch();
						if (c=='w') choice--;
						if (c=='s') choice++;
						if (choice>1) i=choice=0;
						if (choice<0) i=choice=1;
						if (c=='\r')
						switch(choice)
						{
							case 0:insert();//继续录入 
							case 1://返回菜单 
							{
								system("cls");
								printf("正在返回菜单…");
								Sleep(1500);
								menu();
							}
						}
					}
				}
				case 1://不设置密码
				{
					itemArray[num_products].password_setted=0;//将该物品判定为”未设置密码“ 
					num_products++;//更新仓库物品数量 
					choice=0;
					while (1)
					{
						int i=0;
						system("cls");
						printf("是否继续录入？\n\n");
						printf("===============\n");
						for(;i<2;i++)
						{
							if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否继续录入 
							else printf("%s\n",kaishi[i]);
						}
						printf("===============\n"); 
						printf("按w/s选择，按回车确认\n");
						c= _getch();
						if (c=='w') choice--;
						if (c=='s') choice++;
						if (choice>1) i=choice=0;
						if (choice<0) i=choice=1;
						if (c=='\r')
						switch(choice)
						{
							case 0: insert();//继续录入 
							case 1://返回菜单 
							{
								system("cls");
								printf("正在返回菜单…");
								Sleep(1500);
								menu();
							}
						}
					}
				}
			}
		}
	}
}

void change()
{
	system("cls");
	char name1[100];//定义修改的物品名称 
	item *q = itemArray;
    int number1;//定义整型变量，用来判断是否中断修改
    printf("请输入您想修改的物品名称：");
	scanf("%s",&name1); //输入修改的物品名称 
	printf("\n");
	while(1)//利用循环实现修改不同的物品的信息 
	{
		if(strcmp(name1,q->name)==0)//判断该物品是否在仓库中存在 
		{
			printf("已找到该物品：\n\n"); //找到该物品
			if (q->password_setted)//该物品被设置过密码 
			{
				choice=0;
				while (1)
				{
					int i=0;
					system("cls");
					printf("===============\n");
					for(;i<2;i++)
					{
						if (choice==i) printf("%s\n",change_menu_ch[i]);//选择“修改信息”or“取消密码” 
						else printf("%s\n",change_menu[i]);
					}
					printf("===============\n"); 
					printf("按w/s选择，按回车确认\n");
					c= _getch();
					if (c=='w') choice--;
					if (c=='s') choice++;
					if (choice>1) i=choice=0;
					if (choice<0) i=choice=1;
					if (c=='\r')
					switch(choice)
					{
						case 0://选择修改信息 
						{
							while (1)
							{
								system("cls");
								printf("请输入密码：");
								int i = 0; //记录密码长度
								char password_input[18];//用于储存密码 
								char c; //用于实现密码隐式输入
								while (1)
								{
									c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
								    if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
									break; //while 循环的出口
									else if (c == '\b') 
									{ //遇到退格，需要删除前一个星号
										printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
										--i;
									}
									else
									{
										if (i>17||i<5&&c=='\r')//密码长度小于6或大于18 
										{
											printf("\n\n密码须为6-18位！");
											Sleep(1500);
											system("cls");
											printf("请输入密码：");
											for (int j=0;j<i;j++) printf("*");
										}
									    else
										{
											password_input[i++] = c;//将字符放入数组
										    printf("*");
										}
									}
								}
								if (strcmp(password_input,q->password)==0)//密码正确 
								{
									system("cls");
									printf(" %s 的原信息：\n\n",q->name);//输出原物品的信息
									int tmp=q->quantity;
									printf("数量：%d\n",q->quantity);
									printf("成本：%d\n",q->cost);
									printf("价格：%d\n\n",q->price);
									printf("已完成对物品的原信息的输出\n\n");
									printf("请进行修改（若物品信息中有不想修改的请输入原信息）\n\n"); //修改物品的各类信息 
									printf("名称： ");
									scanf("%s",q->name);
									printf("\n数量：");
									scanf("%d",&q->quantity);
									q->record[day]+=q->quantity-tmp;
									if (q->quantity==0)
									{
										for (int i = num_products - 2; i >=q-itemArray; i--) itemArray[i]=itemArray[i + 1]; // 将后面的物品移到前面来填补空位
					        			num_products--; // 更新仓库中现有物品数量
									}
									if (q->quantity)//数量不为0时的修改 
									{
										printf("\n成本：");
										scanf("%d",&q->cost);
										printf("\n价格：");
										scanf("%d",&q->price);
										printf("\n密码：");
										int i=0,lower=0,upper=0,digit=0; //记录密码长度 
										char c; //用于实现密码隐式输入
										while (1)
										{
											c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
											lower+=islower(c); 
											upper+=isupper(c);
											digit+=isdigit(c);//通过密码中大小写字母以及数字是否出现过判断密码强弱 
											if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
											break; //while 循环的出口
											else if (c == '\b') 
											{ //遇到退格，需要删除前一个星号
												printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
												--i;
											}
											else
											{
												if (i>17||i<5&&c=='\r')// 密码长度小于6或大于18 
												{
													printf("\n\n密码须为6-18位！");
													Sleep(1500);
													system("cls");
													printf("密码：");
													for (int j=0;j<i;j++) printf("*");
												}
												else
												{
													q->password[i++] = c;//将字符放入数组
												    printf("*");
											    }
										    }
										}
										if (lower&&upper&&digit) printf("\n\n强密码！");//密码中同时设置大小写字母与数字，判定为强密码 
										else printf("\n\n弱密码！");
									}
									else printf("\n删除成功！");//数量为0时的删除 
									Sleep(1500);
									system("cls");
									printf("修改完成！");//修改完成
									Sleep(1500);
									choice=0;
									while (1)
									{
										int i=0;
										system("cls");
										printf("是否继续修改？\n\n");
										printf("===============\n");
										for(;i<2;i++)
										{
											if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续修改 
											else printf("%s\n",kaishi[i]);
										}
										printf("===============\n"); 
										printf("按w/s选择，按回车确认\n");
										c= _getch();
										if (c=='w') choice--;
										if (c=='s') choice++;
										if (choice>1) i=choice=0;
										if (choice<0) i=choice=1;
										if (c=='\r')
										switch(choice)
										{
											case 0: change();//继续修改 
											case 1://返回菜单 
											{
												system("cls");
												printf("正在返回菜单…");
												Sleep(1500);
												menu();
											}
										}
									}
								}
								else
								{
									printf("\n\n密码错误，请重新输入！");
									Sleep(1500);
									system("cls");
									printf("请输入密码：");
									for (int j=0;j<i;j++) printf("*");
								}		
							}
						}
						case 1://取消密码 
						{
							while (1)
							{
								system("cls");
								printf("请输入密码：");
								int i = 0; //记录密码长度
								char password_input[18];//用于储存密码 
								char c; //用于实现密码隐式输入
								while (1)
								{
									c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
								    if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
									break; //while循环的出口
									else if (c == '\b') 
									{ //遇到退格，需要删除前一个星号
										printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
									    --i;
									}
									else
									{
										if (i>17||i<5&&c=='\r')//密码长度小于6或大于18 
										{
											printf("\n\n密码须为6-18位！");
											Sleep(1500);
											system("cls");
											printf("请输入密码：");
											for (int j=0;j<i;j++) printf("*");
										}
										else
										{
											password_input[i++] = c;//将字符放入数组
											printf("*");
										}
									}
								}
								if (strcmp(password_input,q->password)==0)//密码正确
								{
									q->password_setted=0;//将该物品判定为未设置密码 
									printf("密码已取消！"); 
									Sleep(1500);
									system("cls");
									printf("修改完成");//修改完成
									choice=0;
									while (1)
									{
										int i=0;
										system("cls");
										printf("是否继续修改？\n\n");
										printf("===============\n");
										for(;i<2;i++)
										{
											if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否继续修改 
											else printf("%s\n",kaishi[i]);
										}
										printf("===============\n"); 
										printf("按w/s选择，按回车确认\n");
										c= _getch();
										if (c=='w') choice--;
										if (c=='s') choice++;
										if (choice>1) i=choice=0;
										if (choice<0) i=choice=1;
										if (c=='\r')
										switch(choice)
										{
											case 0: change();//继续修改 
											case 1://返回菜单 
											{
												system("cls");
												printf("正在返回菜单…");
												Sleep(1500);
												menu();
											}
										}
									}
								}
								else
								{
									printf("\n\n密码错误，请重新输入！");
									Sleep(1500);
									system("cls");
									printf("请输入密码：");
									for (int j=0;j<i;j++) printf("*");
								}
							}
						}
					}
				}
			}
			else//该物品未被设置过密码 
			{
				system("cls");
				printf(" %s 的原信息：\n\n",q->name);//输出原物品的信息
				int tmp=q->quantity;
				printf("数量：%d\n",q->quantity);
				printf("成本：%d\n",q->cost);
				printf("价格：%d\n",q->price);
				printf("已完成对物品的原信息的输出\n\n");
				printf("请进行修改（若物品信息中有不想修改的请输入原信息）\n\n"); //修改物品的各类信息 
				printf("名称： ");
				scanf("%s",q->name);
				printf("\n数量：");
				scanf("%d",&q->quantity);
				*(q->record+day)+=q->quantity-tmp;
				if (q->quantity==0)
				{
					for (int i = num_products - 2; i >= q-itemArray; i--) itemArray[i]=itemArray[i + 1]; // 将后面的物品移到前面来填补空位
        			num_products--; // 更新仓库中现有物品数量
				}
				if (q->quantity)//数量不为0时的修改 
				{
					printf("\n成本：");
					scanf("%d",&q->cost);
					printf("\n价格：");
					scanf("%d",&q->price);
					choice=0;
					while (1)
					{
						int i=0;
						system("cls");
						printf("是否设置密码？\n\n");
						printf("===============\n");
						for(;i<2;i++)
						{
							if (choice==i) printf("%s\n",kaishi_ch[i]);
							else printf("%s\n",kaishi[i]);
						}
						printf("===============\n"); 
						printf("按w/s选择，按回车确认\n");
						c= _getch();
						if (c=='w') choice--;
						if (c=='s') choice++;
						if (choice>1) i=choice=0;
						if (choice<0) i=choice=1;
						if (c=='\r')
						switch(choice)
						{
							case 0://设置密码
							{
								system("cls");
								q->password_setted=1;
								printf("请输入密码：");
								int i=0,lower=0,digit=0,upper=0; //记录密码长度
								char c; //用于实现密码隐式输入
								while (1)
								{
									c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
									lower+=islower(c);
									upper+=isupper(c);
									digit+=isdigit(c);//通过密码是否同时含有大小写字母及数字来判断强弱 
									if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
									break; //while 循环的出口
									else if (c == '\b') 
									{ //遇到退格，需要删除前一个星号
									    printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
								        --i;
								    }
								    else
									{
										if (i>17||i<5&&c=='\r')//密码位数大于17小于5 
										{
											printf("\n\n密码须为6-18位！");
											Sleep(1500);
											system("cls");
											printf("请输入密码：");
											for (int j=0;j<i;j++) printf("*");
										}
										else
										{
											q->password[i++] = c;//将字符放入数组
											printf("*");
										}
							    	}
								}
								if (lower&&upper&&digit) printf("\n\n强密码！");//密码中同时设置了大小写字母和数字，判定为强密码 
								else printf("\n\n弱密码！");
								Sleep(1500);
								system("cls");
								printf("修改完成\n");//修改完成
								Sleep(1500);
								choice=0;
								while (1)
								{
									int i=0;
									system("cls");
									printf("是否继续修改？\n\n");
									printf("===============\n");
									for(;i<2;i++)
									{
										if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续修改 
										else printf("%s\n",kaishi[i]);
									}
									printf("===============\n"); 
									printf("按w/s选择，按回车确认\n");
									c= _getch();
									if (c=='w') choice--;
									if (c=='s') choice++;
									if (choice>1) i=choice=0;
									if (choice<0) i=choice=1;
									if (c=='\r')
									switch(choice)
									{
										case 0: change();//继续修改 
										case 1://返回菜单 
										{
											system("cls");
											printf("正在返回菜单…");
											Sleep(1500);
											menu();
										}
									}
								}
						    }
							case 1://不设置密码 
							{
								system("cls");
								printf("修改完成");//修改完成
								Sleep(1500);
								choice=0;
								while (1)
								{
									int i=0;
									system("cls");
									printf("是否继续修改？\n\n");
									printf("===============\n");
									for(;i<2;i++)
									{
										if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续修改 
										else printf("%s\n",kaishi[i]);
									}
									printf("===============\n"); 
									printf("按w/s选择，按回车确认\n");
									c= _getch();
									if (c=='w') choice--;
									if (c=='s') choice++;
									if (choice>1) i=choice=0;
									if (choice<0) i=choice=1;
									if (c=='\r')
									switch(choice)
									{
										case 0: change();//继续修改 
										case 1://返回菜单 
										{
											system("cls");
											printf("正在返回菜单…");
											Sleep(1500);
											menu();
										}
									}
								}
							}
						}
					}
				}
				else//数量为0时删除
				{
					printf("\n删除成功！");
					Sleep(1500);
					system("cls");
					printf("修改完成");
					Sleep(1500);
					choice=0;
					while (1)
					{
						int i=0;
						system("cls");
						printf("是否继续修改？\n\n");
						printf("===============\n");
						for(;i<2;i++)
						{
							if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否继续修改 
							else printf("%s\n",kaishi[i]);
						}
						printf("===============\n"); 
						printf("按w/s选择，按回车确认\n");
						c= _getch();
						if (c=='w') choice--;
						if (c=='s') choice++;
						if (choice>1) i=choice=0;
						if (choice<0) i=choice=1;
						if (c=='\r')
						switch(choice)
						{
							case 0: change();//继续修改 
							case 1://返回菜单 
							{
								system("cls");
								printf("正在返回菜单…");
								Sleep(1500);
								menu();
							}
						}
					}
				}
			}
		}
		else if (q==(itemArray+num_products))//超出仓库原有的物品数量 
		{
			choice=0;
			while (1)
			{
				int i=0;
				system("cls");
				printf("未找到该物品!\n\n");//未找到该物品 
				printf("是否添加？\n\n"); 
				printf("===============\n");
				for(;i<2;i++)
				{
					if (choice==i) printf("%s\n",kaishi_ch[i]);//选择是否添加 
					else printf("%s\n",kaishi[i]);
				}
				printf("===============\n"); 
				printf("按w/s选择，按回车确认\n");
				c= _getch();
				if (c=='w') choice--;
				if (c=='s') choice++;
				if (choice>1) i=choice=0;
				if (choice<0) i=choice=1;
				if (c=='\r')
				switch(choice)
				{
					case 0://选择添加 
					{
						system("cls");
						if (num_products==100)//仓库已满 
						{
							while (1)
							{
								system("cls");
								printf("仓库已满，录入失败!\n\n是否返回菜单？\n\n");
								printf("===============\n");
								printf("* YesYesYes!< *\n");
								printf("===============\n"); 
								printf("按w/s选择，按回车确认\n");
								c= _getch();
								if (c=='\r')
								{
									system("cls");
									printf("正在返回菜单…");
									Sleep(1500);
									menu();
								}
							}
						}
						else//仓库未满 
						{
							strcpy(itemArray[num_products].name,name1);
							//从键盘键入数据 
							system("cls");
							printf("请输入物品数量："); 
							scanf("%d",&itemArray[num_products].quantity);
							system("cls");
							itemArray[num_products].record[day]=itemArray[num_products].quantity;
							printf("请输入物品成本：");
							scanf("%d",&itemArray[num_products].cost);
							system("cls");
							printf("请输入物品价格："); 
							scanf("%d",&itemArray[num_products].price);
							system("cls");
							choice=0;
							while (1)
							{
								int i=0;
								system("cls");
								printf("是否设置密码？\n\n");//是否设置密码 
								printf("===============\n");
								for(;i<2;i++)
								{
									if (choice==i) printf("%s\n",kaishi_ch[i]); 
									else printf("%s\n",kaishi[i]);
								}
								printf("===============\n"); 
								printf("按w/s选择，按回车确认\n");
								c= _getch();
								if (c=='w') choice--;
								if (c=='s') choice++;
								if (choice>1) i=choice=0;
								if (choice<0) i=choice=1;
								if (c=='\r')
								switch(choice)
								{
									case 0://设置密码
									{
										system("cls");
										itemArray[num_products].password_setted=1;//判定该物品已设置密码 
										printf("请输入密码：");
										int i=0,lower=0,digit=0,upper=0; //记录密码长度
										char c; //用于实现密码隐式输入
										while (1)
										{
											c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
											lower+=islower(c);
											upper+=isupper(c);
											digit+=isdigit(c);
											if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
											break; //while 循环的出口
											else if (c == '\b') 
											{ //遇到退格，需要删除前一个星号
											    printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
										        --i;
										    }
										    else
											{
												if (i>17||i<5&&c=='\r')//密码位数小于6或大于18 
												{
													printf("\n\n密码须为6-18位！");
													Sleep(1500);
													system("cls");
													printf("请输入密码：");
													for (int j=0;j<i;j++) printf("*");
												}
												else
												{
													itemArray[num_products].password[i++] = c;//将字符放入数组
													printf("*");
												}
									    	}
										}
										if (lower&&upper&&digit) printf("\n\n强密码！");//密码同时设置大小写字母与数字，判定为强密码 
										else printf("\n\n弱密码！");
										Sleep(1500);
										system("cls");
										num_products++;//更新仓库物品数量 
										choice=0;
										while (1)
										{
											int i=0;
											system("cls");
											printf("是否继续修改？\n\n");
											printf("===============\n");
											for(;i<2;i++)
											{
												if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续修改 
												else printf("%s\n",kaishi[i]);
											}
											printf("===============\n"); 
											printf("按w/s选择，按回车确认\n");
											c= _getch();
											if (c=='w') choice--;
											if (c=='s') choice++;
											if (choice>1) i=choice=0;
											if (choice<0) i=choice=1;
											if (c=='\r')
											switch(choice)
											{
												case 0: change();//继续修改 
												case 1://返回菜单 
												{
													system("cls");
													printf("正在返回菜单…");
													Sleep(1500);
													menu();
												}
											}
										}
									}
									case 1://不设置密码
									{
										itemArray[num_products].password_setted=0;//判定为未设置密码 
										num_products++;//更新仓库物品数量 
										choice=0;
										while (1)
										{
											int i=0;
											system("cls");
											printf("是否继续修改？\n\n");
											printf("===============\n");
											for(;i<2;i++)
											{
												if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续修改 
												else printf("%s\n",kaishi[i]);
											}
											printf("===============\n"); 
											printf("按w/s选择，按回车确认\n");
											c= _getch();
											if (c=='w') choice--;
											if (c=='s') choice++;
											if (choice>1) i=choice=0;
											if (choice<0) i=choice=1;
											if (c=='\r')
											switch(choice)
											{
												case 0: change();
												case 1:
												{
													system("cls");
													printf("正在返回菜单…");
													Sleep(1500);
													menu();
												}
											}
										}
									}
								}
							}
						}
					}
					case 1://放弃添加
					{
						choice=0;
						while (1)
						{
							int i=0;
							system("cls");
							printf("是否继续修改？\n\n");
							printf("===============\n");
							for(;i<2;i++)
							{
								if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续修改 
								else printf("%s\n",kaishi[i]);
							}
							printf("===============\n"); 
							printf("按w/s选择，按回车确认\n");
							c= _getch();
							if (c=='w') choice--;
							if (c=='s') choice++;
							if (choice>1) i=choice=0;
							if (choice<0) i=choice=1;
							if (c=='\r')
							switch(choice)
							{
								case 0: change();//继续修改 
								case 1://返回菜单 
								{
									system("cls");
									printf("正在返回菜单…");
									Sleep(1500); 
									menu();
								}
							}
						}
					}
				} 
			}
		}
		q++;//将修改的物品与原物品对应上 
	}
}

void remove()//物品信息删除 
{
	int j;
	system("cls"); 
	if (num_products)//仓库不空 
	{
		int found=0;
		char name[20];
	    // 输入要删除的物品名称
	    printf("请输入要删除的物品名称: ");
	    scanf("%s", name);
	    system("cls");
	    // 查找要删除的物品，并标记为已找到
	    for (j = 0; j< num_products; j++) 
		{
	        if (strcmp(itemArray[j].name,name) == 0) 
			{
	            found = 1;
	            break;
	        }
	    }
	    // 根据是否找到要删除的物品执行不同的操作
	    if (found)//找到了 
		{
			if (itemArray[j].password_setted)//该物品有密码 
			{
				while (1)
				{
					printf("请输入密码：");
					int i = 0; //记录密码长度
					char password_input[18];//用于储存密码 
					char c; //用于实现密码隐式输入
					while (1)
					{
						c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
					    if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
						break; //while 循环的出口
						else if (c == '\b') 
						{ //遇到退格，需要删除前一个星号
							printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
							--i;
						}
						else
						{
							if (i>17||i<5&&c=='\r')
							{
								printf("\n\n密码须为6-18位！");
								Sleep(1500);
								system("cls");
								printf("请输入密码：");
								for (int j=0;j<i;j++) printf("*");
							}
						    else
							{
								password_input[i++]=c;//将字符放入数组
							    printf("*");
							}
						}
					}
					if (strcmp(password_input,itemArray[j].password)==0)//密码正确 
					{
						for (int i=num_products-2;i>=j; i--) itemArray[i]=itemArray[i + 1]; // 将后面的物品移到前面来填补空位
	        			num_products--; // 更新仓库中现有物品数量
	        			printf("物品删除成功！\n");
	        			Sleep(1500);
	        			choice=0;
						while (1)
						{
							int i=0;
							system("cls");
							printf("是否继续删除？\n\n");
							printf("===============\n");
							for(;i<2;i++)
							{
								if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续删除
								else printf("%s\n",kaishi[i]);
							}
							printf("===============\n"); 
							printf("按w/s选择，按回车确认\n");
							c= _getch();
							if (c=='w') choice--;
							if (c=='s') choice++;
							if (choice>1) i=choice=0;
							if (choice<0) i=choice=1;
							if (c=='\r')
							switch(choice)
							{
								case 0:remove();//继续删除 
								case 1://返回菜单 
								{
									system("cls");
									printf("正在返回菜单…");
									Sleep(1500);
									menu();
								}
							}
						}
					}
					else//密码错误 
					{
						printf("\n\n密码错误，请重新输入！");
						Sleep(1500);
						system("cls");
						printf("请输入密码：");
						for (int j=0;j<i;j++) printf("*");
					} 
				}
			}
			else//该物品无密码 
			{
				for (int i = num_products - 2; i >= j; i--) itemArray[i]=itemArray[i + 1]; // 将后面的物品移到前面来填补空位
	        	num_products--; // 更新仓库中现有物品数量
	        	printf("物品删除成功！\n");
	        	Sleep(1500);
	        	choice=0;
				while (1)
				{
					int i=0;
					system("cls");
					printf("是否继续删除？\n\n");
					printf("===============\n");
					for(;i<2;i++)
					{
						if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续删除 
						else printf("%s\n",kaishi[i]);
					}
					printf("===============\n"); 
					printf("按w/s选择，按回车确认\n");
					c= _getch();
					if (c=='w') choice--;
					if (c=='s') choice++;
					if (choice>1) i=choice=0;
					if (choice<0) i=choice=1;
					if (c=='\r')
					switch(choice)
					{
						case 0: remove();//继续删除 
						case 1://返回菜单 
						{
							system("cls");
							printf("正在返回菜单…");
							Sleep(1500);
							menu();
						}
					}
				}
			}
	    }
		printf("此物品在仓库中不存在！\n\n"); // 如果未找到要删除的物品，给出提示信息
		Sleep(3000);
		while (_getch()=='\r'||_getch()==' ') continue;
		choice=0;
		while (1)
		{
			int i=0;
			system("cls");
			printf("是否继续删除？\n\n");
			printf("===============\n");
			for(;i<2;i++)
			{
				if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续删除 
				else printf("%s\n",kaishi[i]);
			}
			printf("===============\n"); 
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='w') choice--;
			if (c=='s') choice++;
			if (choice>1) i=choice=0;
			if (choice<0) i=choice=1;
			if (c=='\r')
			switch(choice)
			{
				case 0: remove();//继续删除 
				case 1://返回菜单 
				{
					system("cls");
					printf("正在返回菜单…");
					Sleep(1500);
					menu();
				}
			}
		}
	}
	else//仓库为空 
	{
		while (1)//返回菜单 
		{
			system("cls");
			printf("仓库里还没有物品!\n\n是否返回菜单？\n\n");
			printf("===============\n");
			printf("* YesYesYes!< *\n");
			printf("===============\n"); 
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='\r')
			{
				system("cls");
				printf("正在返回菜单…");
				Sleep(1500);
				menu();
			}
		}
	}
}

void search()//查询物品信息 
{
	system("cls");
	char name[100];//定义所查找的物品名称数组 
	printf("请输入您所查询物品的名称：");//通过物品名称来查询
	scanf("%s",&name);
	int number;
	item *p=itemArray;
	while(1)
	{
		if(strcmp(name,p->name)==0)//找到该物品 
		{
			while (1)
			{
				system("cls");
				printf("您所查询的 %s 的信息:\n",p->name);//查询方式
				printf("数量：%d\n",p->quantity);//数量 
				printf("成本：%d\n",p->cost);//成本 
				printf("价格：%d\n\n",p->price);//价格
				printf("增减记录:\n");//增减记录
				for (int i=1;i<=day;i++) p->record[i]>=0?printf("Day%d: +%d\n",i,p->record[i]):printf("Day%d：%d\n",i,p->record[i]);
				printf("\n未来物品数量预测：\n");//未来物品数量预测并保证输出非负数
				double difference_sum=0;
				for (int i=day+1;i<31;i++)  printf("Day%d：\t%.0lf\n",i,(double)(*(p->record+day)-1)+(difference_sum+=gradient_descent(day,i)));
				if (w<0) printf("\n建议提价\n");//供不应求，建议提价 
				else printf("\n建议降价\n");//求不应供，建议降价 
				printf("\n查询成功\n");
				Sleep(3000);
				choice=0;
				while(_getch()=='\r'||_getch()==' ')
    			continue;
				while (1)
				{
					int i=0;
					system("cls");
					printf("您还要继续查询吗\n\n");
					printf("===============\n");
					for(;i<2;i++)
					{
						if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续查询 
						else printf("%s\n",kaishi[i]);
					}
					printf("===============\n"); 
					printf("按w/s选择，按回车确认\n");
					c= _getch();
					if (c=='w') choice--;
					if (c=='s') choice++;
					if (choice>1) i=choice=0;
					if (choice<0) i=choice=1;
					if (c=='\r')
					switch(choice)
					{
						case 0: search();//继续查询 
						case 1://返回菜单 
						{
							system("cls");
							printf("正在返回菜单…");
							Sleep(1500); 
							menu();
						}
					}
				}
			}
		}
		else if (p-itemArray==num_products)
		{
			choice=0;
			while (1)
			{
				int i=0;
				system("cls");
				printf("未找到该物品!\n\n"); //未找到该物品 
				printf("是否添加？\n\n"); 
				printf("===============\n");
				for(;i<2;i++)
				{
					if (choice==i) printf("%s\n",kaishi_ch[i]);//是否添加 
					else printf("%s\n",kaishi[i]);
				}
				printf("===============\n"); 
				printf("按w/s选择，按回车确认\n");
				c= _getch();
				if (c=='w') choice--;
				if (c=='s') choice++;
				if (choice>1) i=choice=0;
				if (choice<0) i=choice=1;
				if (c=='\r')
				switch(choice)
				{
					case 0://选择添加 
					{
						system("cls");
						if (num_products==100)//仓库已满 
						{
							while (1)
							{
								system("cls");
								printf("仓库已满，录入失败!\n\n是否返回菜单？\n\n");//是否返回菜单 
								printf("===============\n");
								printf("* YesYesYes!< *\n");
								printf("===============\n"); 
								printf("按w/s选择，按回车确认\n");
								c= _getch();
								if (c=='\r')//返回菜单 
								{
									system("cls");
									printf("正在返回菜单…");
									Sleep(1500);
									menu();
								}
							}
						}
						else//仓库未满 
						{
							strcpy(itemArray[num_products].name,name);
							//从键盘键入数据 
							system("cls");
							printf("请输入物品数量："); 
							scanf("%d",&itemArray[num_products].quantity);
							system("cls");
							itemArray[num_products].record[day]=itemArray[num_products].quantity;
							printf("请输入物品成本：");
							scanf("%d",&itemArray[num_products].cost);
							system("cls");
							printf("请输入物品价格："); 
							scanf("%d",&itemArray[num_products].price);
							system("cls");
							choice=0;
							while (1)
							{
								int i=0;
								system("cls");
								printf("是否设置密码？\n\n");
								printf("===============\n");
								for(;i<2;i++)
								{
									if (choice==i) printf("%s\n",kaishi_ch[i]);//是否设置密码 
									else printf("%s\n",kaishi[i]);
								}
								printf("===============\n"); 
								printf("按w/s选择，按回车确认\n");
								c= _getch();
								if (c=='w') choice--;
								if (c=='s') choice++;
								if (choice>1) i=choice=0;
								if (choice<0) i=choice=1;
								if (c=='\r')
								switch(choice)
								{
									case 0://设置密码
									{
										system("cls");
										itemArray[num_products].password_setted=1;
										printf("请输入密码：");
										int i=0,lower=0,digit=0,upper=0; //记录密码长度
										char c; //用于实现密码隐式输入
										while (1)
										{
											c = _getch(); //用 _getch() 函数输入，字符不会显示在屏幕上
											lower+=islower(c);
											upper+=isupper(c);
											digit+=isdigit(c);
											if (c == '\r'&&i>4) //遇到回车，表明密码输入结束
											break; //while 循环的出口
											else if (c == '\b') 
											{ //遇到退格，需要删除前一个星号
											    printf("\b \b");  //退格，打一个空格，再退格，实质上是用空格覆盖掉星号
										        --i;
										    }
										    else
											{
												if (i>17||i<5&&c=='\r')//密码位数小于6大于18
												{
													printf("\n\n密码须为6-18位！");
													Sleep(1500);
													system("cls");
													printf("请输入密码：");
													for (int j=0;j<i;j++) printf("*");
												}
												else
												{
													itemArray[num_products].password[i++] = c;//将字符放入数组
													printf("*");
												}
									    	}
										}
										if (lower&&upper&&digit) printf("\n\n强密码！");//密码同时设置大小写字母与数字，为强密码 
										else printf("\n\n弱密码！");//弱密码 
										Sleep(1500);
										system("cls");
										num_products++;//更新仓库物品数量 
										choice=0;
										while (1)
										{
											int i=0;
											system("cls");
											printf("是否继续查询？\n\n");
											printf("===============\n");
											for(;i<2;i++)
											{
												if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续查询 
												else printf("%s\n",kaishi[i]);
											}
											printf("===============\n"); 
											printf("按w/s选择，按回车确认\n");
											c= _getch();
											if (c=='w') choice--;
											if (c=='s') choice++;
											if (choice>1) i=choice=0;
											if (choice<0) i=choice=1;
											if (c=='\r')
											switch(choice)
											{
												case 0: search();//继续查询 
												case 1://返回菜单 
												{
													system("cls");
													printf("正在返回菜单…");
													Sleep(1500);
													menu();
												}
											}
										}
									}
									case 1://不设置密码
									{
										itemArray[num_products].password_setted=0;//将该物品判定为未设置密码 
										num_products++;//更新仓库物品数量 
										choice=0;
										while (1)
										{
											int i=0;
											system("cls");
											printf("是否继续查询？\n\n");
											printf("===============\n");
											for(;i<2;i++)
											{
												if (choice==i) printf("%s\n",kaishi_ch[i]);//是否继续查询 
												else printf("%s\n",kaishi[i]);
											}
											printf("===============\n"); 
											printf("按w/s选择，按回车确认\n");
											c= _getch();
											if (c=='w') choice--;
											if (c=='s') choice++;
											if (choice>1) i=choice=0;
											if (choice<0) i=choice=1;
											if (c=='\r')													
											switch(choice)
											{
												case 0: search();//继续查询 
												case 1://返回菜单 
												{
													system("cls");
													printf("正在返回菜单…");
													Sleep(1500);
													menu();
												}
											}
										}
									}
								}
							}
						}
					}
					case 1://放弃添加 
					{
						choice=0;
						while (1)
						{
							int i=0;
							system("cls");
							printf("是否继续查询？\n\n");
							printf("===============\n");
							for(;i<2;i++)
							{
								if (choice==i) printf("%s\n",kaishi_ch[i]);//继续查询 
								else printf("%s\n",kaishi[i]);
							}
							printf("===============\n"); 
							printf("按w/s选择，按回车确认\n");
							c= _getch();
							if (c=='w') choice--;
							if (c=='s') choice++;
							if (choice>1) i=choice=0;
							if (choice<0) i=choice=1;
							if (c=='\r')
							switch(choice)
							{
								case 0: search();//查询 
								case 1://返回菜单 
								{
									system("cls");
									printf("正在返回菜单…");
									Sleep(1500); 
									menu();
								}
							}
						}
					}
				}
			}
		}
		p++;
	}
}




//传给qsort的函数，用于定义不同的排序方式


 
//按数量升序 
int cmp_quantity_upper(const void *a,const void *b)
{
	item *x=(item*)a;
	item *y=(item*)b;
	return x->quantity-y->quantity;
} 

//按成本升序 
int cmp_cost_upper(const void *a,const void *b)
{
	item *x=(item*)a;
	item *y=(item*)b;
	return x->cost-y->cost;
}

//按价格升序 
int cmp_price_upper(const void *a,const void *b)
{
	item *x=(item*)a;
	item *y=(item*)b;
	return x->price-y->price;
}


//按数量降序 
int cmp_quantity_lower(const void *a,const void *b)
{
	item *x=(item*)a;
	item *y=(item*)b;
	return y->quantity-x->quantity;
}

//按成本降序 
int cmp_cost_lower(const void *a,const void *b)
{
	item *x=(item*)a;
	item *y=(item*)b;
	return y->cost-x->cost;
}

//按价格降序 
int cmp_price_lower(const void *a,const void *b)
{
	item *x=(item*)a;
	item *y=(item*)b;
	return y->price-x->price;
}

void sort()
{
	system("cls");
	int sum=0;
	for (index=0;index<num_products;index++) sum+=itemArray[index].quantity;
	printf("仓库各物品数量和：%d",sum);//打印仓库物品总数 
	Sleep(1500);
	while (_getch()=='\r'||_getch()==' ') continue;
	if (num_products)//仓库不空 
	{
		char sort_menu[5][20]={"* 按数量排序  *","* 按成本排序  *","* 按价格排序  *"},
		   sort_menu_ch[5][20]={"* 按数量排序< *","* 按成本排序< *","* 按价格排序< *"},
		   order_menu[5][15]={"* 升序  *","* 降序  *"},
		order_menu_ch[5][15]={"* 升序< *","* 降序< *"}; 
		system("cls");
	    choice=0;
		while (1)
		{
			int i=0,should_break=0;//循环终止条件判断 
			system("cls");
			printf("===============\n");
			for(;i<3;i++)
			{
				if (choice==i) printf("%s\n",sort_menu_ch[i]);//选择排序方式 
				else printf("%s\n",sort_menu[i]);
			}
			printf("===============\n"); 
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='w') choice--;
			if (c=='s') choice++;
			if (choice>2) i=choice=0;
			if (choice<0) i=choice=2;
			if (c=='\r')
			{
				should_break=1;//已作出选择，将终止循环 
				system("cls");
				switch(choice)
				{
					case 0://按数量排序 
					{
						choice=0;
						while (1)
						{
							int i=0;
							system("cls");
							printf("您已选择 按数量排序\n\n");
							printf("=========\n");
							for(;i<3;i++)
							{
								if (choice==i) printf("%s\n",order_menu_ch[i]);//选择顺序 
								else printf("%s\n",order_menu[i]);
							}
							printf("=========\n"); 
							printf("按w/s选择，按回车确认\n");
							c= _getch();
							if (c=='w') choice--;
							if (c=='s') choice++;
							if (choice>1) i=choice=0;
							if (choice<0) i=choice=1;
							if (c=='\r')
							{
								if (choice==0)
								{
									qsort(itemArray,num_products,sizeof(item),cmp_quantity_upper);//数量升序 
									break;
								}
								else
								{
									qsort(itemArray,num_products,sizeof(item),cmp_quantity_lower);//数量降序 
									break;
								}
							}
						}
						break;
					}
					case 1://按成本排序 
					{
						choice=0;
						while (1)
						{
							int i=0;
							system("cls");
							printf("您已选择 按成本排序\n\n");
							printf("=========\n");
							for(;i<3;i++)
							{
								if (choice==i) printf("%s\n",order_menu_ch[i]);//选择顺序 
								else printf("%s\n",order_menu[i]);
							}
							printf("=========\n"); 
							printf("按w/s选择，按回车确认\n");
							c= _getch();
							if (c=='w') choice--;
							if (c=='s') choice++;
							if (choice>1) i=choice=0;
							if (choice<0) i=choice=1;
							if (c=='\r')
							{
								if (choice==0)
								{
									qsort(itemArray,num_products,sizeof(item),cmp_cost_upper);//成本升序 
									break;
								}
								else
								{
									qsort(itemArray,num_products,sizeof(item),cmp_cost_lower);//成本降序 
									break;
								}
							}
						}
						break;
					}
					case 2://按价格排序 
					{
						choice=0;
						while (1)
						{
							int i=0;
							system("cls");
							printf("=========\n");
							for(;i<3;i++)
							{
								if (choice==i) printf("%s\n",order_menu_ch[i]);//选择顺序 
								else printf("%s\n",order_menu[i]);
							}
							printf("=========\n"); 
							printf("按w/s选择，按回车确认\n");
							c= _getch();
							if (c=='w') choice--;
							if (c=='s') choice++;
							if (choice>1) i=choice=0;
							if (choice<0) i=choice=1;
							if (c=='\r')
							{
								if (choice==0)
								{
									qsort(itemArray,num_products,sizeof(item),cmp_price_upper);//价格升序 
									break;
								}
								else
								{
									qsort(itemArray,num_products,sizeof(item),cmp_price_lower);//价格降序 
									break;
								}
							}
						}
					}
				}
			}
			if (should_break) break;//终止循环 
		}
		item *p=itemArray+1;
		while (1)
		{
			system("cls");
			char page[5][20]={"* 上一页      *","* 返回菜单    *","* 下一页      *"},page_ch[5][20]={"* 上一页<     *","* 返回菜单<   *","* 下一页<     *"};
			printf("第%d页\n\n",p-itemArray);//当前是第p-itemArray页 
			printf("名称：%s\n",p->name);//名称 
			printf("数量：%d\n",p->quantity);//数量 
			printf("成本：%d\n",p->cost);//成本 
			printf("价格：%d\n\n",p->price);//价格 
			printf("增减记录：\n");//增减记录 
			for (int i=1;i<=day;i++) p->record[i]>0?printf("\nDay%d：\t+%d\n",i,p->record[i]):printf("\nDay%d：\t%d\n",i,p->record[i]); 
			printf("\n未来物品数量预测：\n\n");//物品数量预测并保证输出非负数 
			double difference_sum=0;
			for (int i=day+1;i<31;i++) printf("Day%d：\t%.0lf\n",i,(double)(*(p->record+day))+(difference_sum+=gradient_descent(day,i)));
			if (w<0) printf("\n建议提价\n\n");//供不应求，建议提价 
			else printf("\n建议降价\n\n");//求不应供，建议降价 
			Sleep(3000);
			while (_getch()=='\r'||_getch()==' ') continue;
			system("cls");
			choice=0;
			if (num_products==1)//物品总数为1 
			{
				while (1)
				{
					system("cls");
					printf("===============\n");
					printf("* 返回菜单<   *\n");
					printf("===============\n"); 
					printf("按w/s选择，按回车确认\n");
					c= _getch();
					if (c=='\r')//返回菜单 
					{
					system("cls");
					printf("正在返回菜单…");
					Sleep(1500);
					menu();
					}
				}
			}
			else//物品总数不为1 
			{
				if (p==itemArray)//当前为第一个物品 
				{
					choice=0;
					while (1)
					{
						system("cls");
						int i=0;
						printf("===============\n");
						for (;i<2;i++)
						{
							if (choice==i) printf("%s\n",page_ch[i+1]);//选择“返回菜单”或“下一页” 
							else printf("%s\n",page[i+1]);
						}
						printf("===============\n"); 
						printf("按w/s选择，按回车确认\n");
						c= _getch();
						if (c=='w') choice--;
						if (c=='s') choice++;
						if (choice>1) i=choice=0;
						if (choice<0) i=choice=1;
						if (c=='\r')
						{
							if (choice==1)//下一页 
							{
								p++;
								break;
							}
							else if (choice==0)//返回菜单 
							{
								system("cls");
								printf("正在返回菜单…");
								Sleep(1500);
								menu();
							}
						}
					}					
				}
				else if (p==itemArray+num_products-1)//当前物品为最后一个物品 
				{
					choice=0;
					while (1)
					{
						system("cls");
						printf("===============\n");
						int i=0;
						for (;i<2;i++)
						{
							if (choice==i) printf("%s\n",page_ch[i]);//选择“上一页”或“返回菜单” 
							else printf("%s\n",page[i]);
						}
						printf("===============\n"); 
						printf("按w/s选择，按回车确认\n");
						c= _getch();
						if (c=='w') choice--;
						if (c=='s') choice++;
						if (choice>1) i=choice=0;
						if (choice<0) i=choice=1;
						if (c=='\r')
						{
							if (choice==0)//选择“上一页” 
							{
								p--;
								break;
							}
							else if (choice==1)//选择”返回菜单“ 
							{
								system("cls");
								printf("正在返回菜单…");
								Sleep(1500);
								menu();
							}
						}
					}
				}
				else
				{
					choice=0;
					while (1)
					{
						system("cls");
						printf("===============\n");
						int i=0;
						for (;i<3;i++)
						{
							if (choice==i) printf("%s\n",page_ch[i]);//选择“上一页”或“返回菜单”或“下一页” 
							else printf("%s\n",page[i]);
						}
						printf("===============\n"); 
						printf("按w/s选择，按回车确认\n");
						c= _getch();
						if (c=='w') choice--;
						if (c=='s') choice++;
						if (choice>2) i=choice=0;
						if (choice<0) i=choice=2;
						if (c=='\r')
						{
							if (choice==0)//选择“上一页” 
							{
								p--;
								break;
							}
							else if (choice==1)//选择“返回菜单” 
							{
								system("cls");
								printf("正在返回菜单…");
								Sleep(1500);
								menu();
							}
							else if (choice==2)//选择“下一页” 
							{
								p++;
								break;
							}
						}
					}
				}
			}
		}
	}
	else//仓库为空
	{
		while (1)
		{
			system("cls");
			printf("仓库里还没有物品!\n\n是否返回菜单？\n\n");//是否返回菜单 
			printf("===============\n");
			printf("* YesYesYes!< *\n");
			printf("===============\n"); 
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='\r')//返回菜单 
			{
				system("cls");
				printf("正在返回菜单…");
				Sleep(1500);
				menu();
			}
		}
	}
}

void income()//查询仓库收益 
{
	double difference_sum_yesterday=0,difference_sum_day=0,total_income=0;;
	system("cls");
	if (day>1)
	{
		for (item *p=itemArray;p<itemArray+num_products;p++)
		total_income+=-(p->price-p->cost)*(((double)(*(p->record+day))-(double)(*(p->record+day-1))));
		//计算当天收益并打印
		printf("收益为%d元\n\n",total_income);
		printf("\n未来仓库收益预测：\n\n");//预测未来收益并打印 
		for (int i=day;i<31;i++)
		{
			double total_income=0;
			for (item *p=itemArray;p<itemArray+num_products;p++)
			{
				double difference_sum_yesterday=0,difference_sum_day=0;
				total_income+=(p->price-p->cost)*(double)(*(p->record+day))+(difference_sum_day+=gradient_descent(day,i))-(double)(*(p->record+day-1))-(difference_sum_yesterday+=gradient_descent(day-1,i));
			}
			printf("Day%d：\t%.2lf元\n",i,total_income);
		}
		Sleep(3000);
		while (_getch()=='\r'||_getch()==' ') continue;
		while(1) 
		{
			system("cls");
			printf("是否返回菜单？\n"); 
			printf("===============\n");
			printf("* YesYesYes!< *\n");
			printf("===============\n"); 
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='\r')//返回菜单 
			{
				write();
				system("cls");
				printf("正在返回菜单…");
				Sleep(1500);
				menu();
			}
		}
	}
	else
	{
		while (1)
		{
			system("cls");
			printf("仓库还没有收益!\n\n是否返回菜单？\n\n");
			printf("===============\n");
			printf("* YesYesYes!< *\n");
			printf("===============\n"); 
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='\r')//返回菜单 
			{
				system("cls");
				printf("正在返回菜单…");
				Sleep(1500);
				menu();
			}
		}
	}
}

void date_change()
{
	char date_ch[5][15]={"* 日期+1< *","* 日期-1< *"},date[5][15]={"* 日期+1  *","* 日期-1  *"};
	choice=0;
	if (day==1)//第一天 
	{
		while (1)
		{
			system("cls");
			printf("===========\n");
			printf("* 日期+1< *\n");
			printf("===========\n");
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='\r')//日期+1 
			{
				day++;
				break;
			}
		}
	}
	else if (day==30)//第三十天
	{
		while (1)//日期-1 
		{
			system("cls");
			printf("===========\n");
			printf("* 日期-1< *\n");
			printf("===========\n");
			printf("按w/s选择，按回车确认\n");
			c= _getch();
			if (c=='\r')
			{
				day--;
				break;
			}
		}
	}
	else
	while (1)
	{
		int i=0;
		system("cls");
		printf("===========\n");
		for(;i<2;i++)
		{
			if (choice==i) printf("%s\n",date_ch[i]);//选择“日期+1”或“日期-1” 
			else printf("%s\n",date[i]);
		}
		printf("===========\n"); 
		printf("按w/s选择，按回车确认\n");
		c= _getch();
		if (c=='w') choice--;
		if (c=='s') choice++;
		if (choice>1) i=choice=0;
		if (choice<0) i=choice=1;
		if (c=='\r')
		{
			if (choice) day--;//日期-1 
			else day++;//日期+1 
			break;
		}
	} 
	system("cls");
	printf("日期已更新，当前为Day%d",day);
	Sleep(1500);
	menu(); 
}

//打印功能菜单
void menu()
{
							//0				//1				//2				//3				//4				//5				//6				//7
	char main_menu[50][150]={"物品信息录入","物品信息查询","物品信息修改","物品信息删除","查询所有物品信息","查询仓库收益","修改日期","结束程序"};
	char main_menu_ch[50][150]={"物品信息录入<","物品信息查询<","物品信息修改<","物品信息删除<","查询所有物品信息<","查询仓库收益<","修改日期<","结束程序<"};
	choice=0;
	char c;
	while (1)
	{
		int i=0;
		system("cls");
		printf("============仓库管理系统=============\n");//打印菜单栏 
		printf("*                                   *\n");
		for(;i<8;i++)
		{
			if (choice==i) printf("*           %-24s*\n",main_menu_ch[i]);
			else printf("*           %-24s*\n",main_menu[i]);
		}
		printf("*                               Day%d*\n",day);
		printf("=====================================\n"); 
		printf("按w/s选择，按回车确认\n");
		c= _getch();
		if (c=='w') choice--;
		if (c=='s') choice++;
		if (choice>8) i=choice=0;
		if (choice<0) i=choice=7;
		if (c=='\r')
		switch(choice)
		{
			case 0: insert();//执行物品信息录入操作 
			case 1: search();//执行物品信息查询操作 
			case 2: change();//执行物品信息修改操作
			case 3: remove();//执行物品信息删除操作 
			case 4: sort(); break;//执行查询所有物品信息操作 
			case 5: income(); break;//执行查询仓库收益操作 
			case 6://执行修改日期操作 
			date_change(); break;
			case 7://执行结束程序操作 
			{
				write();
				fp=fopen("D:/data.dat","wb+");
				fwrite(&day,sizeof(int),1,fp);
				fclose(fp);
				system("cls");
				printf("正在保存数据……\n");
				Sleep(1500);
				printf("\n数据已保存，感谢您的使用！");
				Sleep(1500);
				while (_getch()=='\r'||_getch()==' ') continue;
				exit(0);
			}
		}
	}
}





//主函数 
int main()
{
	fp=fopen("D:/data.dat","wb+");
	fread(&day,sizeof(int),1,fp);
	fclose(fp);
	read();
	system("color f");//将控制台字体设置为亮白色 
	menu();
	return 0;
}
```
