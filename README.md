# help
# 1
%{
#include <stdio.h>
FILE *yyin;
%}

%%
"int"|"float"|"char"|"if"|"else"|"while"|"for"|"return"|"void" {
    printf("Keyword\t%s\n", yytext);
}

[0-9]+ {
    printf("Number\t%s\n", yytext);
}

[a-zA-Z_][a-zA-Z0-9_]* {
    printf("Identifier\t%s\n", yytext);
}

"=="|"!="|"<="|">="|"="|"+"|"-"|"*"|"/"|"<"|">" {
    printf("Operator\t%s\n", yytext);
}

";"|","|"("|")"|"{"|"}"|"["|"]" {
    printf("Symbol\t%s\n", yytext);
}

[ \t\n]+ ;

. {
    printf("Unknown\t%s\n", yytext);
}
%%

int yywrap(void)
{
    return 1;
}

int main(void)
{
    yyin = fopen("input.c", "r");

    if (yyin == NULL)
    {
        printf("Cannot open file\n");
        return 1;
    }

    yylex();
    fclose(yyin);
    return 0;
}

#2

%{
#include <stdio.h>
FILE *yyin;
%}

%%
"int"|"float"|"char"|"if"|"else"|"while"|"for"|"return"|"void" {
    printf("Keyword\t%s\n", yytext);
}

[0-9]+ {
    printf("Number\t%s\n", yytext);
}

[a-zA-Z_][a-zA-Z0-9_]* {
    printf("Identifier\t%s\n", yytext);
}

"=="|"!="|"<="|">="|"="|"+"|"-"|"*"|"/"|"<"|">" {
    printf("Operator\t%s\n", yytext);
}

";"|","|"("|")"|"{"|"}"|"["|"]" {
    printf("Symbol\t%s\n", yytext);
}

[ \t\n]+ ;

. {
    printf("Unknown\t%s\n", yytext);
}
%%

int yywrap(void)
{
    return 1;
}

int main(void)
{
    yyin = fopen("input.c", "r");

    if (yyin == NULL)
    {
        printf("Cannot open file\n");
        return 1;
    }

    yylex();
    fclose(yyin);
    return 0;
}
# 3

%{
#include <stdio.h>
int lines = 0, words = 0, chars = 0;
%}
%%
\n          { lines++; chars++; }
[ \t]+      { chars += yyleng; }
[^ \t\n]+   { words++; chars += yyleng; }
%%
int yywrap()
{
    return 1;
}
int main()
{
    printf("Enter text (Press Ctrl+D on Linux/Mac or Ctrl+Z then Enter on Windows to end):\n");
    yylex();

    printf("\nNumber of lines      : %d\n", lines);
    printf("Number of words      : %d\n", words);
    printf("Number of characters : %d\n", chars);
    return 0;
}


#7

id.l
%{
#include "y.tab.h"
%}

%%

[a-zA-Z][a-zA-Z0-9]*    { return ID; }

.                       { return yytext[0]; }

%%

int yywrap()
{
    return 1;
}
id.y
%{
#include <stdio.h>
#include <stdlib.h>

void yyerror(const char *s);
int yylex();
%}

%token ID

%%

S : ID
    {
        printf("Valid Identifier\n");
    }
    ;

%%

void yyerror(const char *s)
{
    printf("Invalid Identifier\n");
}

int main()
{
    printf("Enter an identifier: ");
    yyparse();
    return 0;
}

# 10

#include <stdio.h>

#define MAX 20

int n;
int e[MAX][MAX];       
int visited[MAX];

void epsilonClosure(int state)
{
    int i;
    visited[state] = 1;
    printf("q%d ", state);

    for(i = 0; i < n; i++)
    {
        if(e[state][i] && !visited[i])
        {
            epsilonClosure(i);
        }
    }
}

int main()
{
    int i, j, m;
    int from, to;

    printf("Enter number of states: ");
    scanf("%d", &n);

    // Initialize transition matrix
    for(i = 0; i < n; i++)
        for(j = 0; j < n; j++)
            e[i][j] = 0;

    printf("Enter number of epsilon transitions: ");
    scanf("%d", &m);

    printf("Enter epsilon transitions (from to):\n");
    for(i = 0; i < m; i++)
    {
        scanf("%d %d", &from, &to);
        e[from][to] = 1;
    }

    printf("\nEpsilon Closures:\n");

    for(i = 0; i < n; i++)
    {
        for(j = 0; j < n; j++)
            visited[j] = 0;

        printf("ε-Closure(q%d) = { ", i);
        epsilonClosure(i);
        printf("}\n");
    }

    return 0;
}

#11

#include <stdio.h>

#define MAX 20

int n, symbols;
int epsilon[MAX][MAX];
int trans[MAX][MAX][MAX];
int closure[MAX][MAX];

void findClosure(int state, int start)
{
    int i;

    if (closure[start][state])
        return;

    closure[start][state] = 1;

    for (i = 0; i < n; i++)
    {
        if (epsilon[state][i])
            findClosure(i, start);
    }
}

void calculateClosures()
{
    int i;

    for (i = 0; i < n; i++)
        findClosure(i, i);
}

int main()
{
    int i, j, k, s;
    int m, from, to;

    printf("Enter number of states: ");
    scanf("%d", &n);

    printf("Enter number of input symbols: ");
    scanf("%d", &symbols);

    for (i = 0; i < n; i++)
    {
        for (j = 0; j < n; j++)
        {
            epsilon[i][j] = 0;
            closure[i][j] = 0;

            for (s = 0; s < symbols; s++)
                trans[s][i][j] = 0;
        }
    }

    printf("Enter number of epsilon transitions: ");
    scanf("%d", &m);

    printf("Enter epsilon transitions (from to):\n");

    for (i = 0; i < m; i++)
    {
        scanf("%d %d", &from, &to);
        epsilon[from][to] = 1;
    }

    calculateClosures();

    printf("\nEpsilon Closures:\n");

    for (i = 0; i < n; i++)
    {
        printf("E-Closure(q%d) = { ", i);

        for (j = 0; j < n; j++)
        {
            if (closure[i][j])
                printf("q%d ", j);
        }

        printf("}\n");
    }

    for (s = 0; s < symbols; s++)
    {
        printf("\nEnter transitions for input symbol %d.\n", s);
        printf("Enter number of transitions: ");
        scanf("%d", &m);

        printf("Enter transitions (from to):\n");

        for (i = 0; i < m; i++)
        {
            scanf("%d %d", &from, &to);
            trans[s][from][to] = 1;
        }
    }

    printf("\nNFA without epsilon transitions:\n");

    for (s = 0; s < symbols; s++)
    {
        printf("\nFor input symbol %d:\n", s);

        for (i = 0; i < n; i++)
        {
            int result[MAX] = {0};

            for (j = 0; j < n; j++)
            {
                if (closure[i][j])
                {
                    for (k = 0; k < n; k++)
                    {
                        if (trans[s][j][k])
                        {
                            int p;

                            for (p = 0; p < n; p++)
                            {
                                if (closure[k][p])
                                    result[p] = 1;
                            }
                        }
                    }
                }
            }

            printf("delta(q%d, %d) = { ", i, s);

            for (j = 0; j < n; j++)
            {
                if (result[j])
                    printf("q%d ", j);
            }

            printf("}\n");
        }
    }

    return 0;
}

# 12

NFA to DFA*/
#include <stdio.h>

#define MAX 20

int n;
int trans[MAX][2][MAX];
int dfa[MAX][2];
int dfaStates[MAX][MAX];
int dfaCount = 0;

int findState(int set[])
{
    int i, j, same;

    for (i = 0; i < dfaCount; i++)
    {
        same = 1;

        for (j = 0; j < n; j++)
        {
            if (dfaStates[i][j] != set[j])
            {
                same = 0;
                break;
            }
        }

        if (same)
            return i;
    }

    return -1;
}

int addState(int set[])
{
    int i;

    for (i = 0; i < n; i++)
        dfaStates[dfaCount][i] = set[i];

    return dfaCount++;
}

void printSet(int set[])
{
    int i;

    printf("{ ");

    for (i = 0; i < n; i++)
    {
        if (set[i])
            printf("q%d ", i);
    }

    printf("}");
}

int main()
{
    int i, j, k, x;
    int count, to, start;
    int next[MAX];

    printf("Enter number of NFA states: ");
    scanf("%d", &n);

    for (i = 0; i < n; i++)
    {
        for (j = 0; j < 2; j++)
        {
            printf("\nFrom q%d on input %d", i, j);
            printf("\nEnter number of destination states: ");
            scanf("%d", &count);

            for (k = 0; k < count; k++)
            {
                scanf("%d", &to);
                trans[i][j][to] = 1;
            }
        }
    }

    printf("\nEnter start state: ");
    scanf("%d", &start);

    for (i = 0; i < n; i++)
        next[i] = 0;

    next[start] = 1;
    addState(next);

    for (i = 0; i < dfaCount; i++)
    {
        for (j = 0; j < 2; j++)
        {
            for (k = 0; k < n; k++)
                next[k] = 0;

            for (k = 0; k < n; k++)
            {
                if (dfaStates[i][k])
                {
                    for (x = 0; x < n; x++)
                    {
                        if (trans[k][j][x])
                            next[x] = 1;
                    }
                }
            }

            int state = findState(next);

            if (state == -1)
                state = addState(next);

            dfa[i][j] = state;
        }
    }

    printf("\nDFA Transition Table\n");
    printf("-----------------------------\n");
    printf("State\t\t0\t1\n");
    printf("-----------------------------\n");

    for (i = 0; i < dfaCount; i++)
    {
        printf("D%d = ", i);
        printSet(dfaStates[i]);
        printf("\tD%d\tD%d\n", dfa[i][0], dfa[i][1]);
    }

    return 0;
}


#13

First and Follow*/
#include <stdio.h>
#include <string.h>
#include <ctype.h>

char prod[10][10];
char first[10][10], follow[10][10];
int n;

void add(char set[], char c)
{
    if (!strchr(set, c))
    {
        int l = strlen(set);
        set[l] = c;
        set[l + 1] = '\0';
    }
}

void FIRST(char c)
{
    int i;
    char x;

    if (!isupper(c))
    {
        add(first[c - 'A'], c);
        return;
    }

    for (i = 0; i < n; i++)
    {
        if (prod[i][0] == c)
        {
            x = prod[i][2];

            if (x == '#')
                add(first[c - 'A'], '#');

            else if (!isupper(x))
                add(first[c - 'A'], x);

            else
                FIRST(x);
        }
    }
}

void FOLLOW(char c)
{
    int i, j;
    char x;

    if (c == prod[0][0])
        add(follow[c - 'A'], '$');

    for (i = 0; i < n; i++)
    {
        for (j = 2; prod[i][j] != '\0'; j++)
        {
            if (prod[i][j] == c)
            {
                if (prod[i][j + 1] != '\0')
                {
                    x = prod[i][j + 1];

                    if (!isupper(x))
                        add(follow[c - 'A'], x);
                    else
                    {
                        for (int k = 0; first[x - 'A'][k] != '\0'; k++)
                            if (first[x - 'A'][k] != '#')
                                add(follow[c - 'A'],
                                    first[x - 'A'][k]);
                    }
                }
                else if (prod[i][0] != c)
                {
                    FOLLOW(prod[i][0]);

                    for (int k = 0; follow[prod[i][0] - 'A'][k] != '\0'; k++)
                        add(follow[c - 'A'],
                            follow[prod[i][0] - 'A'][k]);
                }
            }
        }
    }
}

int main()
{
    int i;

    printf("Enter number of productions: ");
    scanf("%d", &n);

    printf("Enter productions (example: E=TR):\n");

    for (i = 0; i < n; i++)
        scanf("%s", prod[i]);
    for (i = 0; i < n; i++)
        FIRST(prod[i][0]);
    for (i = 0; i < n; i++)
        FOLLOW(prod[i][0]);

    printf("\nFIRST:\n");

    for (i = 0; i < n; i++)
    {
        char c = prod[i][0];

        printf("FIRST(%c) = { ", c);

        for (int j = 0; first[c - 'A'][j] != '\0'; j++)
            printf("%c ", first[c - 'A'][j]);

        printf("}\n");
    }

    printf("\nFOLLOW:\n");

    for (i = 0; i < n; i++)
    {
        char c = prod[i][0];

        printf("FOLLOW(%c) = { ", c);

        for (int j = 0; follow[c - 'A'][j] != '\0'; j++)
            printf("%c ", follow[c - 'A'][j]);

        printf("}\n");
    }

    return 0;
}


/*Mathew Tito
18
Recursive Descent Parser*/
#include <stdio.h>
#include <string.h>
#include <ctype.h>

char input[100];
int pos = 0;

void E();
void E_prime();
void T();
void T_prime();
void F();


void match(char expected)
{
    if (input[pos] == expected)
    {
        pos++;
    }
    else
    {
        printf("Error: Expected '%c'\n", expected);
        exit(1);
    }
}

void E()
{
    T();
    E_prime();
}

void E_prime()
{
    if (input[pos] == '+')
    {
        match('+');
        T();
        E_prime();
    }
}

void T()
{
    F();
    T_prime();
}

void T_prime()
{
    if (input[pos] == '*')
    {
        match('*');
        F();
        T_prime();
    }

}


void F()
{
    if (input[pos] == '(')
    {
        match('(');
        E();
        match(')');
    }
    else if (input[pos] == 'i' && input[pos + 1] == 'd')
    {
        pos += 2;  
    }
    else
    {
        printf("Error: Expected 'id' or '('\n");
        exit(1);
    }
}

int main()
{
    printf("Enter the expression: ");
    scanf("%s", input);

    E();

    if (input[pos] == '\0')
        printf("String is accepted.\n");
    else
        printf("String is rejected.\n");

    return 0;
}


/Mathew Tito
18
Shift Reduce Parser*/
#include <stdio.h>
#include <string.h>

char stack[100];
int top = -1;

void push(char c)
{
    stack[++top] = c;
}

void pop()
{
    if (top >= 0)
        top--;
}

void displayStack()
{
    int i;

    for (i = 0; i <= top; i++)
        printf("%c", stack[i]);
}

int reduce()
{

    if (top >= 1 && stack[top - 1] == 'i' && stack[top] == 'd')
    {
        pop();
        pop();
        push('E');
        return 1;
    }


    if (top >= 2 &&
        stack[top - 2] == '(' &&
        stack[top - 1] == 'E' &&
        stack[top] == ')')
    {
        pop();
        pop();
        pop();
        push('E');
        return 1;
    }


    if (top >= 2 &&
        stack[top - 2] == 'E' &&
        stack[top - 1] == '+' &&
        stack[top] == 'E')
    {
        pop();
        pop();
        pop();
        push('E');
        return 1;
    }


    if (top >= 2 &&
        stack[top - 2] == 'E' &&
        stack[top - 1] == '*' &&
        stack[top] == 'E')
    {
        pop();
        pop();
        pop();
        push('E');
        return 1;
    }

    return 0;
}

int main()
{
    char input[100];
    int i = 0;

    printf("Enter the input string: ");
    scanf("%s", input);

    printf("\n%-20s %-20s\n", "Stack", "Action");
    printf("----------------------------------------\n");

    while (input[i] != '\0')
    {

        push(input[i]);

        printf("%-20s SHIFT %c\n", "", input[i]);
        displayStack();
        printf("\n");

        i++;


        while (reduce())
        {
            displayStack();
            printf("                    REDUCE\n");
        }
    }


    while (reduce())
    {
        displayStack();
        printf("                    REDUCE\n");
    }

    printf("\n");

    if (top == 0 && stack[0] == 'E')
        printf("Input string is ACCEPTED.\n");
    else
        printf("Input string is REJECTED.\n");

    return 0;
}

/*Mathew Tito
18 
Intermediate Code Generation*/
#include <stdio.h>
#include <string.h>
#include <ctype.h>

int tempCount = 1;


void newTemp(char *temp)
{
    sprintf(temp, "t%d", tempCount++);
}

int main()
{
    char expr[100];
    char result[100];

    printf("Enter an expression (example: a=b+c*d): ");
    scanf("%s", expr);

    int len = strlen(expr);


    int equalPos = -1;

    for (int i = 0; i < len; i++)
    {
        if (expr[i] == '=')
        {
            equalPos = i;
            break;
        }
    }

    if (equalPos == -1)
    {
        printf("Invalid expression.\n");
        return 1;
    }


    strcpy(result, "");
    for (int i = 0; i < equalPos; i++)
    {
        int l = strlen(result);
        result[l] = expr[i];
        result[l + 1] = '\0';
    }


    char rhs[100];
    strcpy(rhs, expr + equalPos + 1);





    for (int i = 0; rhs[i] != '\0'; i++)
    {
        if (rhs[i] == '*' || rhs[i] == '/')
        {
            char left = rhs[i - 1];
            char op = rhs[i];
            char right = rhs[i + 1];

            char temp[10];
            newTemp(temp);

            printf("%s = %c %c %c\n",
                   temp, left, op, right);




            char newRhs[100];
            int k = 0;

            for (int j = 0; j < i - 1; j++)
                newRhs[k++] = rhs[j];

            for (int j = 0; temp[j] != '\0'; j++)
                newRhs[k++] = temp[j];

            for (int j = i + 2; rhs[j] != '\0'; j++)
                newRhs[k++] = rhs[j];

            newRhs[k] = '\0';

            strcpy(rhs, newRhs);


            i = -1;
        }
    }

  
    for (int i = 0; rhs[i] != '\0'; i++)
    {
        if (rhs[i] == '+' || rhs[i] == '-')
        {
            char left = rhs[i - 1];
            char op = rhs[i];
            char right = rhs[i + 1];

            char temp[10];
            newTemp(temp);

            printf("%s = %c %c %c\n",
                   temp, left, op, right);

            char newRhs[100];
            int k = 0;

            for (int j = 0; j < i - 1; j++)
                newRhs[k++] = rhs[j];

            for (int j = 0; temp[j] != '\0'; j++)
                newRhs[k++] = temp[j];

            for (int j = i + 2; rhs[j] != '\0'; j++)
                newRhs[k++] = rhs[j];

            newRhs[k] = '\0';

            strcpy(rhs, newRhs);

            i = -1;
        }
    }


    printf("%s = %s\n", result, rhs);

    return 0;
}







