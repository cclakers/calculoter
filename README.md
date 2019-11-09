# calculoter
209.11.9
/*utility.h*/
#pragma once
#include<iostream>
#include"lk_stack.h"
using namespace std;
template<class ElemType>
class Calculator
{
private:
	LinkStack<ElemType> opnd;
	LinkStack<char> optr;
	int OperPrior(char op);
	void Get20perands(ElemType &left, ElemType &right);
	ElemType Operate(ElemType left, char op, ElemType right);
	bool IsOperator(char ch);
public:
	Calculator() {};
	virtual ~Calculator() {};
	void Run();
};
template<class ElemType>
int Calculator<ElemType>::OperPrior(char op)
{
	int prior;
	switch (op)
	{
	case '=':prior = 0; return prior; break;
	case '(':prior = 1; return prior; break;
	case ')':prior = 1; return prior; break;
	case '+':
	case '-':prior = 2; return prior; break;
	case '*':
	case '/':prior = 3; return prior; break;
	case '^':prior = 4; return prior; break;
	default:
		return -1;
		break;
	}
}
template<class ElemType>
void Calculator<ElemType>::Get20perands(ElemType &left, ElemType &right)
{
	if (opnd.Pop(left) == UNDER_FLOW) throw Error("表达式有错");
	if (opnd.Pop(right) == UNDER_FLOW) throw Error("表达式有错");
}
template<class ElemType>
ElemType Calculator<ElemType>::Operate(ElemType left, char op, ElemType right)
{
	switch (op)
	{
	case'+':opnd.Push(left + right);
		return left + right;
		break;
	case'-':opnd.Push(left - right);
		return left - right;
		break;
	case'*':opnd.Push(left * right);
		return left * right;
		break;
	case'/':opnd.Push(left / right);
		return left / right;
		break;
	case'^':opnd.Push(pow(left, right));
		return pow(left, right);
		break;
	}
}
template<class ElemType>
bool Calculator<ElemType>::IsOperator(char ch)
{
	if (ch == '=' || ch == '(' || ch == ')' || ch == '+' || ch == '-' || ch == '*' || ch == '/' || ch == '^')
	{
		return true;
	}
	else return false;
}
template<class ElemType>
void Calculator<ElemType>::Run()
{
	optr.Clear();
	opnd.Clear();
	optr.Push('=');
	char ch;//临时字符
	char PriorChar;//前一字符
	char optrTop;//栈顶字符
	ElemType operand;//操作数
	char op;//操作符
	PriorChar = '=';
	ch = GetChar();
	if (optr.Top(optrTop) == UNDER_FLOW) throw Error("表达式有错");
	while (optrTop != '=' || ch != '=')
	{
		if (isdigit(ch) || ch == '.')
		{
			cin.putback(ch);//将字符ch放回输入流
			cin >> operand;
			opnd.Push(operand);
			PriorChar = '0';
			ch = GetChar();
		}
		else if (!IsOperator(ch))
		{
			cout << "表达式有错" << endl;
		}
		else
		{
			if (PriorChar == '=' ||( PriorChar == '(' && (ch == '+' || ch == '-')))
			{
				opnd.Push(0);
				PriorChar = '0';
			}
			if ((optrTop == ')' && ch == '(' )||( optrTop == '(' && ch == '=') ||( optrTop == '='
				&& ch == ')')) throw Error("表达式错误");
			else if (optrTop == '(' && ch == ')')
			{
				if (optr.Pop(optrTop) == UNDER_FLOW) throw Error("表达式有错！");
				ch = GetChar();
				PriorChar = ')';
			}
			else if (ch == '(' ||(OperPrior(optrTop) < OperPrior(ch)))
			{
				optr.Push(ch);
				PriorChar = ch;
				ch = GetChar();
			}
			else
			{
				if (optr.Pop(op) == UNDER_FLOW) throw Error("表达式错误!");
				ElemType left, right;
				Get20perands(left, right);
				Operate(left, op, right);
			}
		}
		if (optr.Top(optrTop) == UNDER_FLOW) throw Error("表达式错误");
	}
	if (opnd.Top(operand) == UNDER_FLOW) throw Error("表达式有错！");
		cout << operand;
}
