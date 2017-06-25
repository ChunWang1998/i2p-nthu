# Week 14
---
### qsort 

Reference:
http://www.gnu.org/software/libc/manual/html_node/Array-Sort-Function.html#Array-Sort-Function

�ϥ� qsort
void qsort (void *array, size_t count, size_t size, comparison_fn_t compare);

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int compare_int (const void *a, const void *b)
{
    const int *va = (const int *) a;
    const int *vb = (const int *) b;
    return *va-*vb;
}

int compare_double (const void *a, const void *b)
{
    const double *da = (const double *) a;
    const double *db = (const double *) b;
    return (*da > *db) - (*da < *db);
}

int main(void)
{
    int data1[SIZE];
    double data2[SIZE];
    int i;

    for (i=0; i<SIZE; i++) {
        data1[i] = rand()%SIZE;
        data2[i] = (double) rand()/RAND_MAX;
    }

    printf("original: ");
    for (i=0; i<SIZE; i++) {
        printf("%d ", data1[i]);
    }
    printf("\n");

    printf("  sorted: ");
    qsort(data1, SIZE, sizeof(int), compare_int);
    for (i=0; i<SIZE; i++) {
        printf("%d ", data1[i]);
    }
    printf("\n");

    printf("original: ");
    for (i=0; i<SIZE; i++) {
        printf("%.2f ", data2[i]);
    }
    printf("\n");

    printf("  sorted: ");
    qsort(data2, SIZE, sizeof(double), compare_double);
    for (i=0; i<SIZE; i++) {
        printf("%.2f ", data2[i]);
    }
    printf("\n");

    return 0;
}
```

### ��T�w���ת��r���}�C�Ƨ�

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int main(void)
{
    char strs[SIZE][4] ={
        "aab", "abc", "aaa", "abb", "acb",
        "aab", "abc", "aaa", "abb", "acb"
    };
    int i;

    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }
    printf("\n");
    qsort(strs, SIZE, 4*sizeof(char), (int (*) (const void *, const void *))strcmp);
    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }

    return 0;
}
```

�W������k�i�H���T�B�@����]�O�r����שT�w�ӥB�W�ߦa��m
strs[SIZE][4] �o�ӤG���}�C�����e
strs[0] ������ "aab"
strs[1] ������ "abc"
���O���o�˪��G���}�C
�b�O���餤���M�O�Τ@���覡�`�ǩ�m

table           |   ptr[0]  |   ptr[1]  |   ptr[2]  |   ptr[3]  
----------------|-----------|-----------|-----------|-----------
ptr = strs[0]   |   `'a'`   |   `'a'`   |   `'b'`   |   `'0'`   
ptr = strs[1]   |   `'a'`   |   `'b'`   |   `'c'`   |   `'0'`   
ptr = strs[2]   |   `'a'`   |   `'a'`   |   `'a'`   |   `'0'`   
ptr = strs[3]   |   `'a'`   |   `'b'`   |   `'b'`   |   `'0'`   
......          |           |           |           |           

�}�C�`�@��10�Ӥ����A�C�Ӱ}�C�������]�t�T�ӭ^��r���~�[�᭱��ۤ@�� '\0' �r�� �`�@ 4 bytes
�]���ڭ̥i�H�� qsort(strs, SIZE, 4*sizeof(char), (int (*) (const void *, const void *))strcmp);
�� qsort �H�}�C�������򥻳����ڭ̱Ƨ� �]�N�O�H 4 bytes �����i��ӧO�������h��
�̫� strs �}�C�����e�|�����Q�ק� �÷h�ʦ��Ѥp�ƨ�j
qsort �ĥ|�ӰѼƭn�j��O�ഫ
�� strcmp �ܦ��ŦX���O
�쥻 strcmp �����O int strcmp (const char *s1, const char *s2);
�g�L���O�ഫ�����ܦ� int strcmp (const void *s1, const void *s2);

�t�@�ؤ覡
�z�L���а}�C
�i�H�Φb���P���ת��r��Ƨ�

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int compare_str_ptr(const void *a, const void *b)
{
    char **pa;
    char **pb;
    pa = (char **) a;
    pb = (char **) b;
    return strcmp(*pa, *pb);
}

int main(void)
{
    char strs[SIZE][4] ={
        "aab", "abc", "aaa", "abb", "acb",
        "aab", "abc", "aaa", "abb", "acb"
    };
    char *ptrs[SIZE];
    int i;

    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }
    printf("\n");


    for (i=0; i<SIZE; i++) {
        ptrs[i] = strs[i];
    }
    qsort(ptrs, SIZE, sizeof(char*), compare_str_ptr);
    for (i=0; i<SIZE; i++) {
        printf("%s\n", ptrs[i]);
    }
    printf("\n");
    for (i=0; i<SIZE; i++) {
        printf("%s\n", strs[i]);
    }


    return 0;
}
```

ptrs �O�@�ӫ��а}�C
�]�� ptrs ���C�Ӥ������O�@�ӫ���  ���i�H�ΨӰO���Y�ӰO�����m
�ڭ̥��� ptrs ���C�Ӥ��� ptrs[i] ���O�O��C�Ӧr�ꪺ�}�l��}
for (i=0; i<SIZE; i++) {
    ptrs[i] = strs[i];
}

���U�ӹ���а}�C�� qsort
�̷� ptrs ���C�Ӥ����ҫ��쪺�r��j�p
�N ptrs �������h�� 
�ҥH�u�O�մ����Ъ�����
��ڪ���� strs ���|�Q���
�Ъ`�N�o�ɭ� compare ��ƪ��g�k
int compare_str_ptr(const void *a, const void *b)
{
    char * *pa;
    char * *pb;
    pa = (char **) a;
    pb = (char **) b;
    return strcmp(*pa, *pb);
}

�аO��U����ӭ��I:
1. �Q�h�ʪ��F��O����
2. �ΨӤ���h�O���Щҫ��쪺�r��
�ڭ̥i�H���Ӯ榡��ܰ}�C���e
���橳�U�����{���X�|��X
strs: aab0|abc0|aaa0|abb0|acb0|aab0|abc0|aaa0|abb0|acb0|
ptrs: 0028FEE4|0028FEE8|0028FEEC|0028FEF0|0028FEF4|0028FEF8|0028FEFC|0028FF00|0028FF04|0028FF08|
after sorting
ptrs: 0028FF00|0028FEEC|0028FEE4|0028FEF8|0028FEF0|0028FF04|0028FEE8|0028FEFC|0028FF08|0028FEF4|
ptrs: aaa0|aaa0|aab0|aab0|abb0|abb0|abc0|abc0|acb0|acb0|
strs: aab0|abc0|aaa0|abb0|acb0|aab0|abc0|aaa0|abb0|acb0|

��ӰO�����}�H�� strs �����e�N�|�ݥX�ݭ�

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define SIZE 10

int compare_str_ptr(const void *a, const void *b)
{
    char **pa;
    char **pb;
    pa = (char **) a;
    pb = (char **) b;
    return strcmp(*pa, *pb);
}

int main(void)
{
    char strs[SIZE][4] ={
        "aab", "abc", "aaa", "abb", "acb",
        "aab", "abc", "aaa", "abb", "acb"
    };
    char *ptrs[SIZE];
    int i;

    printf("strs: ");
    for (i=0; i<SIZE; i++) {
        printf("%s0|", strs[i]);
    }
    printf("\n");

    for (i=0; i<SIZE; i++) {
        ptrs[i] = strs[i];
    }

    printf("ptrs: ");
    for (i=0; i<SIZE; i++) {
        printf("%p|", ptrs[i]);
    }
    printf("\nafter sorting\n");

    qsort(ptrs, SIZE, sizeof(char*), compare_str_ptr);

    printf("ptrs: ");
    for (i=0; i<SIZE; i++) {
        printf("%p|", ptrs[i]);
    }
    printf("\n");

    printf("ptrs: ");
    for (i=0; i<SIZE; i++) {
        printf("%s0|", ptrs[i]);
    }
    printf("\n");

    printf("strs: ");
    for (i=0; i<SIZE; i++) {
        printf("%s0|", strs[i]);
    }

    return 0;
}
```

### Pointers to Functions

```C
#include <stdio.h>
#include <stdlib.h>
int sum(int a[], int n)
{
    int i, ans = 0;
    for (i=0; i<n; i++) {
        ans += a[i];
    }
    return ans;
}
int sum_squared(int a[], int n)
{
    int i, ans = 0;
    for (i=0; i<n; i++) {
        ans += a[i]*a[i];
    }
    return ans;
}

int middle(int a[], int n)
{
    return a[n/2];
}

int run (  int  (*fp) (int *, int )  , int a[], int n)
{
    return fp(a, n);
}

int main(void)
{
    int a[] = {1, 2, 3, 4};
    printf("%d\n", run(sum, a, 4));
    printf("%d\n", run(sum_squared, a, 4));
    printf("%d\n", run(middle, a, 4));
    return 0;
}
```

### �b�{������������o�O����

```C
/* E10_15.c */
#include <stdio.h>
#include <stdlib.h>
int main(void)
{
   double *ptd;
   int array_size, i;

   printf("How many doubles do you want? ");
   scanf("%d", &array_size);
   ptd = (double *) malloc(array_size * sizeof (double));
   if (ptd == NULL) {
      printf("Memory allocation failed.\n");
      exit(EXIT_FAILURE);
   }

   for (i = 0; i < array_size; i++) {
      ptd[i] = (double) rand() / RAND_MAX;
   }
   for (i = 0; i < array_size; i++) {
      if (*(ptd+i) > 0.5)
         printf("%d: %f\n", i, ptd[i]);
   }

   free(ptd);

   return 0;
}
```

```C
/* W10_09.c */
#include <stdio.h>
int main(void)
{
   int *a, **b, i, j, rows, cols;
   
   scanf("%d %d", &rows, &cols);
   a = (int *) malloc(rows * cols * sizeof(int));
   for (i = 0; i < rows*cols; i++) a[i] = i;
   b = (int* *) malloc(rows * sizeof(int *));

   for (i = 0 ; i < rows; i++) b[i] = &a[i*cols];


   for (i = 0; i < rows; i++) {
      for (j = 0; j < cols; j++) {
         printf("%3d ", b[i][j]); 
      }
      printf("\n");
   }
   
   free(b);
   free(a);

   return 0;
}
```

memory leak

dangling pointer

�r���}�C�M�r��

Reference:
https://stackoverflow.com/questions/2245664/what-is-the-type-of-string-literals-in-c-and-c
http://en.cppreference.com/w/c/language/string_literal
http://en.cppreference.com/w/cpp/language/string_literal

²�n�ӻ��A���C�y���G
1.  string literal�����O�Ochar[]
2.  �p�G�O�Ϋ��Хh���V�Y��string literal�A�������V�����e�O���i�g��
    `char* str = "Test"; str[0] = 't'; // Undefined behavior`
3.  ���F�קK�������~�o�͡A�q�`�|��ĳ�ΡG
    *  `const char* str = "string"; // �p�G�g�X str[0]='t'�A�b�sĶ�ɴN�|�X��`
    *  `char str[] = "string"; // str[0]='S' �O�i�H��`

```
#include <stdio.h>

int main(void)
{

    char *str1[] = {"piece", "of", "cake"};

    char str2[][8] = {"piece", "of", "cake"};

    int i, j;

    for (i=0; i<3; i++) {

        for (j=0; j<8; j++)

            printf("%c", str1[i][j]);

        printf("\n");

    }

    for (i=0; i<3; i++) {

        for (j=0; j<8; j++)

            printf("%c", str2[i][j]);

        printf("\n");

    }

    return 0; 

}
```

### ��ӬP��

```C
#include <stdio.h>
#include <stdlib.h>

void malloc_float2( float * * p , unsigned int sz)
{
    *p = (float *) malloc(sz*sizeof(float));
}

int main(void)
{
    float * ptr = NULL;
    int i;
    int n = 100;

    malloc_float2(&ptr, n);

    for (i=0; i<n; i++)
        ptr[i] = (float) rand()/RAND_MAX;

    free(ptr);

    return 0;
}
```

### C Structures

```C
struct t_point {
    int x;
    int y;
};
typedef struct t_point Point;
```

�W�����g�k
�]�i�H�X�֦�

```C
typedef struct {
    int x;
    int y;
} Point;
```

���F Point �o�� struct ���O����
�N��Υ������ܼ�
�Ҧp
Point pt = {5, 7};
Point *pp;

pp = &pt;
(*pp).x = 10;
pp->x = 10;

���U�O�@�Ӹ����㪺�d��

```C
#include <stdio.h>
#include <stdlib.h>
/* �w�q�@�ӷs�����O */
/* ���W�s�� Point */
/* �̭��]�t x �M y ��� members */ 
/* �w�q�L����   Point �i�H�Q���ӷ�@�@�몺���O�Өϥ� */
/* �]�A�ŧi�s���ܼ�   �άO�ŧi function */ 
typedef struct {
    int x;
    int y;
} Point;

/* ones_vec_1() �|�Ǧ^�Y�� Point ���c����} */
/* �o�Ӧ�}�O�� Point ���c�զ����}�C���}�Y��} */ 
Point * ones_vec_1(int length);

void ones_vec_2(int length, Point **bp);

int main(void)
{
   Point *a, *b;
   int i, length;
   
   printf("The size of a Point is %u bytes.\n", sizeof(Point));
    
   printf("vector length: ");
   scanf("%d", &length);

   /* �Q�� ones_vec_1 ���o�@�Ӱ}�C */
   /* �}�C���C�Ӥ����O�@�� Point */
   /* �}�C���}�Y��}�O���b�����ܼ� a �̭� */    
   a = ones_vec_1(length);
   ones_vec_2(length, &b);

   /* a �O�ӫ����ܼ�   ���O�����O�Y�Ӱ}�C���}�Y��} */
   /* �}�C���C�Ӥ����O a[i] (�O�� Point) */
   /* �ҥH����� members  ���O�O a[i].x �M a[i].y */   
   for (i=0; i<length; i++) 
      printf("(%d, %d) ", a[i].x, a[i].y);
   printf("\n");
   
   for (i=0; i<length; i++)
      printf("(%d, %d) ", b[i].x, b[i].y);
   printf("\n");
   
   
   return 0;
}

Point * ones_vec_1(int length)
{
   Point *a;
   int i;
   a = (Point *) malloc(length * sizeof(Point));
   for (i = 0 ; i < length; i++) {
      a[i].x = 1;
      a[i].y = 1;
   } 
   return a;
}

void ones_vec_2(int length, Point **bp)
{
   int i;
   
   *bp = (Point *) malloc(length * sizeof(Point));
   for (i = 0 ; i < length; i++) {
      (*bp)[i].x = 1;
      (*bp)[i].y = 1;      
   } 
}
```

```C
#include <stdio.h>
#include <stdlib.h>
typedef struct t_complex {
    double r;
    double i;
} Complex;

void add(Complex a, Complex b, Complex *t)
{
    t->r = a.r+b.r;
    t->i = a.i+b.i;
}
void set_complex(Complex *p, double r, double i)
{
    p->r = r;
    p->i = i;
}
void show_complex(Complex t)
{
    printf("%.2f%+.2fi\n", t.r, t.i);
}

int main(void)
{
    Complex x, y, z;
    set_complex(&x, 1, 2);
    set_complex(&y, 2, -3);
    add(x, y, &z);
    show_complex(z);

    return 0;
}
```