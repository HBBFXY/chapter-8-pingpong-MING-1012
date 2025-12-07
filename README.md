[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/wcdVzLDF)
# pythonAssignment_pingPong
## 乒乓竞技分析。借鉴实例 13 思路，采用乒乓球规则模拟比赛，分析体育竞技的规律。
#include<iostream>
#include<string>
#define ERROR -1
#define MAXSIZE 100
using namespace std;

typedef struct BiTNode {
    char data;
    struct BiTNode *lchild, *rchild;
} BiTNode, *BiTree;

int Precede(char a, char b) {
    if (a == '+' || a == '-') {
        if (b == '+' || b == '-' || b == ')' || b == '#') {
            return 1;
        } else {
            return -1;
        }
    } else if (a == '*' || a == '/') {
        if (b == '+' || b == '-' || b == '*' || b == '/' || b == ')' || b == '#') {
            return 1;
        } else {
            return -1;
        }
    } else if (a == '(') {
        if (b == ')') {
            return 0;
        } else {
            return -1;
        }
    } else if (a == '#') {
        if (b == '#') {
            return 0;
        } else {
            return -1;
        }
    }
    return -1; // default
}

BiTree CreateExpTree(char ch) {
    BiTree T = new BiTNode;
    T->data = ch;
    T->lchild = T->rchild = nullptr;
    return T;
}

BiTree InitExpTree() {
    char OPTR[MAXSIZE];
    BiTree EXPT[MAXSIZE];
    int topOPTR = 0;
    int topEXPT = -1;
    OPTR[topOPTR] = '#';
    char ch;
    cin >> ch;

    while (ch != '#' || OPTR[topOPTR] != '#') {
        if (ch >= '0' && ch <= '9') {
            BiTree T = CreateExpTree(ch);
            EXPT[++topEXPT] = T;
            cin >> ch;
        } else {
            if (ch == '(') {
                OPTR[++topOPTR] = ch;
                cin >> ch;
            } else if (ch == ')') {
                while (OPTR[topOPTR] != '(') {
                    char op = OPTR[topOPTR];
                    topOPTR--;
                    BiTree right = EXPT[topEXPT];
                    topEXPT--;
                    BiTree left = EXPT[topEXPT];
                    topEXPT--;
                    BiTree T = CreateExpTree(op);
                    T->lchild = left;
                    T->rchild = right;
                    EXPT[++topEXPT] = T;
                }
                topOPTR--; // pop '('
                cin >> ch;
            } else {
                while (Precede(OPTR[topOPTR], ch) == 1) {
                    char op = OPTR[topOPTR];
                    topOPTR--;
                    BiTree right = EXPT[topEXPT];
                    topEXPT--;
                    BiTree left = EXPT[topEXPT];
                    topEXPT--;
                    BiTree T = CreateExpTree(op);
                    T->lchild = left;
                    T->rchild = right;
                    EXPT[++topEXPT] = T;
                }
                if (ch != '#') {
                    OPTR[++topOPTR] = ch;
                    cin >> ch;
                }
            }
        }
    }

    while (topOPTR > 0) {
        char op = OPTR[topOPTR];
        topOPTR--;
        BiTree right = EXPT[topEXPT];
        topEXPT--;
        BiTree left = EXPT[topEXPT];
        topEXPT--;
        BiTree T = CreateExpTree(op);
        T->lchild = left;
        T->rchild = right;
        EXPT[++topEXPT] = T;
    }

    return EXPT[topEXPT];
}

int EvaluateExTree(BiTree T) {
    if (T == nullptr) {
        return 0;
    }
    if (T->lchild == nullptr && T->rchild == nullptr) {
        return T->data - '0';
    }
    int leftVal = EvaluateExTree(T->lchild);
    int rightVal = EvaluateExTree(T->rchild);
    char op = T->data;
    switch (op) {
        case '+': return leftVal + rightVal;
        case '-': return leftVal - rightVal;
        case '*': return leftVal * rightVal;
        case '/': return leftVal / rightVal;
        default: return 0;
    }
}

int main() {
    BiTree T = InitExpTree();
    cout << EvaluateExTree(T) << endl;
    return 1;
}
