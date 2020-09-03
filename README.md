# Make-a-little-progress-every-day
练习代码编写
//驾校学时管理系统，内含删除，修改，排序，插入，查找功能
#include <stdio.h>
#include <conio.h>
#include <Windows.h>
#include "stdlib.h"
//菜单栏结构体 
typedef enum Menu
{
	Exit = '0',
	Add = '1',
	List = '2',
	Delete = '3',
	Search = '4',
	Change = '5',
	Sort_Asc_Sore = '6',
}MENU;

//学员信息结构体 
typedef struct Driving
{
	char  cName[20]; //姓名
	char  cNumber[10];//学号
	float hours;//学时

	struct Driving  *pNext;
}DRV, *PDRV;


PDRV pHead = NULL;	//信息学员头结点
PDRV pNew = NULL;	//待添加学员信息

size_t iCount = 0;	//学员总数


void Init();//进入界面 
void MainMenu();//主菜单
int MenuSelect();//读取数据
void ReadData();//保存数据
void SaveData();//返回上一级
void Quit();//展示所有学员信息 
void Show_Information();
void Add_Information();//增加学员信息 
void Sort_Asc_Sore_Information();//排序学员 
void Delete_Information();//删除学员信息 
void Search_Information();//查找学员信息 
void Change_Information();//修改学员学时 
void Swap(PDRV stu1, PDRV stu2);//交换


int main()   //主函数 
{
	Init();
	MainMenu();
	return 0;
}


void MainMenu()//主界面 
{

		switch (MenuSelect())	//菜单选择
		{
		case Add:		//添加学员信息
			system("cls");
			Add_Information();
			Quit();
			break;

		case List:		//显示所有学员
			system("cls");
			Show_Information();
			Quit();
			break;

		case Exit:		//退出程序
			system("cls");
			SaveData();
			exit(0);
			break;

		case Sort_Asc_Sore: //学时降序
			system("cls");
			Sort_Asc_Sore_Information();
			Show_Information();
			Quit();
			break;

		case Delete:	//删除学员信息
			system("cls");
			Delete_Information();
			Quit();
			break;
		case Search:	//查询学员信息
			system("cls");
			Search_Information();
			Quit();
			break;

		case Change:	//修改学员信息
			system("cls");
			Change_Information();
			Quit();
			break;
		}
	
}


void Init()//初始化数据 
{
	pHead = (PDRV)malloc(sizeof(DRV));
	pHead->pNext = NULL;

	ReadData();
}

int MenuSelect()
{
	char c;

	do
	{
		system("cls");
		printf("\t\t\t\t\t   *********驾校学时管理系统********* \n");
		printf("\t\t\t\t\t\t   0.安全退出 \n");
		printf("\t\t\t\t\t\t   1.增加录入学员信息 \n");
		printf("\t\t\t\t\t\t   2.输出所有学员信息 \n");
		printf("\t\t\t\t\t\t   3.删除学员 \n");
		printf("\t\t\t\t\t\t   4.查找学员 \n");
		printf("\t\t\t\t\t\t   5.修改学员学时 \n");
		printf("\t\t\t\t\t\t   6.按学时升序排序 \n");

		printf("\t\t\t\t\t\t  请输入您的选择(0-6):");

		c = getchar();//选择菜单

	} while (c < '0' || c > '6');

	return c;
}


void ReadData()//读取数据
{
	FILE* fRead = fopen("drivingInfo.txt", "r");
	if (fRead == NULL)
	{
		SaveData();	//文件不存在就创建文件
		return;
	}
	rewind(fRead);	//将文件内部指针移回文件开头
	while (1)
	{
		pNew = (PDRV)malloc(sizeof(DRV));	//申请一块内存
		fread(pNew, sizeof(DRV), 1, fRead);	//1 表示读取一个DRV字节大小单元
		if (feof(fRead)) break;				//文件末尾退出
											//头插法插入数据

		pNew->pNext = pHead->pNext;
		pHead->pNext = pNew;
		pNew = NULL;
		iCount++;
	}
	fclose(fRead);
}


void SaveData()//返回上一级
{
	FILE* fWrite = fopen("drivingInfo.txt", "w");
	if (fWrite == NULL)
	{
		system("cls");
		printf("保存失败...\n");
		Quit();
	}

	PDRV pCurrent = pHead->pNext;	//指向第一个节点 如果链表为 NULL pCurrent为 NULL
	while (pCurrent != NULL)		//遍历所有学员信息	
	{
		fwrite(pCurrent, sizeof(DRV), 1, fWrite);
		pCurrent = pCurrent->pNext;	//指向下一个节点
	}
	pCurrent = NULL;
	fclose(fWrite);
}

void Quit()//返回 
{
	int i = 0;
	char ch;
	printf("\n\t ");
	for (i = 0; i < 50; i++)
	{
		printf("* ");
		Sleep(10);
	}
	printf("\n\n\t\t\t\t 任意键返回...\n");
	ch = _getch();

	//重新进入主菜单
	MainMenu();
}

void Show_Information()//统计学员信息
{
	PDRV pCurrent = pHead->pNext;	//指向第一个节点 如果链表为 NULL pCurrent为 NULL
	printf("\t\t\t**********************共有: %d 学员.********************\n\n\n", iCount);
	printf("\t\t\t姓名\t学号\t学时\n\n");
	while (pCurrent != NULL)	//遍历输出所有学员
	{
		printf("\t\t\t%s\t%s\t%.1f\n", pCurrent->cName, pCurrent->cNumber, pCurrent->hours);
		pCurrent = pCurrent->pNext;	//指向下一个节点
	}
	pCurrent = NULL;
	printf("\n\n\n");
}

void Add_Information()//添加学员信息
{
	system("CLS");

	pNew = (PDRV)malloc(sizeof(DRV));
	printf("\n\n\n\n\t\t\t\t\t\t 姓名:");
	scanf("%s", pNew->cName);
	printf("\n\t\t\t\t\t\t 学号:");
	scanf("%s", pNew->cNumber);
	printf("\n\t\t\t\t\t\t 学时:");
	scanf("%f", &pNew->hours);

	PDRV pCurrent = pHead;	//指向头结点

	while (pCurrent->pNext != NULL)	//遍历输出所有学员
	{
		if (strcmp(pCurrent->pNext->cNumber, pNew->cNumber) == 0)
		{
			//存在则不添加
			printf("该学号已经存在！");
			return;
		}
		pCurrent = pCurrent->pNext;	//指向下一个节点
	}

	pNew->pNext = pHead->pNext;
	pHead->pNext = pNew;
	pNew = NULL;
	iCount++;
}



void Sort_Asc_Sore_Information()//排序学员 
{
	if (iCount < 2)		//一个学员不需要排序
	{
		return;
	}
	//从小到大排序 冒泡排序
	PDRV pCurrent, pTemp;
	DRV Temp;
	for (pCurrent = pHead->pNext; pCurrent != NULL; pCurrent = pCurrent->pNext)
	{
		for (pTemp = pCurrent->pNext; pTemp != NULL; pTemp = pTemp->pNext)
		{
			if (pCurrent->hours > pTemp->hours)
			{
				Swap(&Temp, pCurrent);
				Swap(pCurrent, pTemp);
				Swap(pTemp, &Temp);
			}
		}
	}
}

void Delete_Information()//删除学员信息 
{

	char ID[50];
	char operate;
	printf("\t\t\t\t\t\t 请输入学号：");
	scanf("%s", ID);
	//遍历学员信息
	PDRV pCurrent = pHead;	//指向头结点 

	while (pCurrent->pNext != NULL)	//遍历输出所有学员
	{
		if (strcmp(pCurrent->pNext->cNumber, ID) == 0)
		{
			//信息库里面有要删除的学员信息
			printf("\t\t\t姓名\t学号\t学时\n\n");
			printf("\t\t\t%s\t%s\t%.1f\n", pCurrent->pNext->cName, pCurrent->pNext->cNumber, pCurrent->pNext->hours);

			printf("\n\n\n\t\t\t\t\t Delete? (y/Enter):");
			operate = _getch();
			if (operate == 'y' || operate == 'Y' || operate == 13)	//13是回车键Enter的ASCII值
			{
				//删除学员信息
				PDRV pTemp = pCurrent->pNext;	//定义PDRV指针 pTemp 指向要删除的节点
				pCurrent->pNext = pTemp->pNext;
				free(pTemp);
				iCount--;
				printf("\n\n\n\t\t\t\t\t ok...\n\n");
				return;
			}
			else {
				printf("\n\n\n\t\t\t\t\t error...\n\n");
				return;
			}
		}
		pCurrent = pCurrent->pNext;	//指向下一个节点
	}
	printf("\n\n\n\n\t\t\t\t\t   没有该学员 ......\n\n");
}



void Search_Information()//查找学员信息 
{

	char ID[50];
	printf("\t\t\t\t\t\t 请输入学号：");
	scanf("%s", ID);
	//遍历学员信息
	PDRV pCurrent = pHead->pNext;	//指向第一个节点 如果链表为 NULL pCurrent为 NULL
	while (pCurrent != NULL)		//遍历所有学员
	{
		if (strcmp(pCurrent->cNumber, ID) == 0)
		{
			printf("\t\t\t姓名\t学号\t学时\n\n");
			printf("\t\t\t%s\t%s\t%.1f\n", pCurrent->cName, pCurrent->cNumber, pCurrent->hours);

			return;
		}
		pCurrent = pCurrent->pNext;	//指向下一个节点
	}
	pCurrent = NULL;
	printf("\n\n\n\n\t\t\t\t\t  没有该学员 .....\n\n");
}


void Change_Information()//修改学员信息 
{
	char ID[50];
	printf("\t\t\t\t\t\t 请输入学号：");
	scanf("%s", ID);
	//遍历学员信息
	PDRV pCurrent = pHead->pNext;	//指向第一个节点 如果链表为 NULL pCurrent为 NULL
	while (pCurrent != NULL)		//遍历所有学员
	{
		if (strcmp(pCurrent->cNumber, ID) == 0)
		{
			printf("\t\t\t姓名\t学号\t学时\n\n");
			printf("\t\t\t%s\t%s\t%.1f\n", pCurrent->cName, pCurrent->cNumber, pCurrent->hours);

			printf("\n\t\t\t\t\t\t 请输入修改后的 学时:");
			scanf("%f", &pCurrent->hours);
			return;
		}
		pCurrent = pCurrent->pNext;	//指向下一个节点
	}
	pCurrent = NULL;
	printf("\n\n\n\n\t\t\t\t\t  没有该学员 .....\n\n");
}

void Swap(PDRV stu1, PDRV stu2)   //交换
{
	strcpy(stu1->cName, stu2->cName);
	strcpy(stu1->cNumber, stu2->cNumber);
	stu1->hours = stu2->hours;
}
