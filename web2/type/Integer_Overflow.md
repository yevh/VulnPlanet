# Integer Overflow

The code below is vulnureable to Integer Overflow


```
#include<stdio.h>
#include<stdlib.h>
#include<string.h>

struct Image
{
	char header[4];
	int width;
	int height;
	char data[10];
};

int ProcessImage(char* filename){

	FILE *fp;
	char ch;
	struct Image img;

	fp = fopen(filename,"r"); 

	if(fp == NULL)
	{
		printf("\nCan't open file or file doesn't exist.");
		exit(0);
	}

	printf("\n\tHeader\twidth\theight\tdata\t\r\n");

	while(fread(&img,sizeof(img),1,fp)>0){
		printf("\n\t%s\t%d\t%d\t%s\r\n",img.header,img.width,img.height,img.data);
	
		int size1 = img.width + img.height; //Vulnerability: integer overflow
		char* buff1=(char*)malloc(size1);

		memcpy(buff1,img.data,sizeof(img.data)); //Vulnerability: no data buffer size/malloc success check?
		free(buff1);
	
		if (size1/2==0){
			free(buff1); //Vulnerability: double free
		}
		else{
			if(size1 == 123456){
				buff1[0]='a'; //Vulnerability: use after free
			}
		}

		int size2 = img.width - img.height+100; //Vulnerability: integer underflow
		//printf("Size1:%d",size1);
		char* buff2=(char*)malloc(size2);

		memcpy(buff2,img.data,sizeof(img.data));

		int size3= img.width/img.height;
		//printf("Size2:%d",size3);

		char buff3[10];
		char* buff4 =(char*)malloc(size3);
		memcpy(buff4,img.data,sizeof(img.data));

		char OOBR_stack = buff3[size3+100]; //Vulnerability: out of bound read (stack)
		char OOBR_heap = buff4[100];

		buff3[size3+100]='c'; //Vulnerability: out of bound write (Stack)
		buff4[100]='c'; //Vulnerability: out of bound write (Heap)

		if(size3>10){
				buff4=0; //memory leak?
		}
		else{
			free(buff4);
		}

		free(buff2);
	}
	fclose(fp);
}

int main(int argc,char **argv)
{
	ProcessImage(argv[1]);
}
```

# Why it's vulnerable?

1. Integer overflow: In the line int size1 = img.width + img.height;, if the sum of img.width and img.height is larger than the maximum value that can be stored in an int variable, it will cause an integer overflow, which could lead to undefined behavior.

2. No data buffer size/malloc success check: In the line memcpy(buff1,img.data,sizeof(img.data));, there is no check to ensure that the malloc() call succeeded in allocating memory for buff1, nor is there a check to ensure that the size of img.data is not larger than the size of buff1. This could potentially lead to a buffer overflow.

3. Double free: In the lines if (size1/2==0){ free(buff1); }, if size1 is an even number, buff1 will be freed, but it is also freed later on in the code. This could potentially lead to a double free error, where memory is freed twice, leading to undefined behavior.

4. Use after free: In the lines if(size1 == 123456){ buff1[0]='a'; }, if size1 is equal to 123456, buff1 will be accessed after it has been freed, which could potentially lead to a use after free error, where memory that has been freed is accessed or modified.

5. Integer underflow: In the line int size2 = img.width - img.height+100;, if the difference of img.width and img.height is smaller than 100, it will cause an integer underflow, which could lead to undefined behavior.

6. Out of bound read (stack): In the line char OOBR_stack = buff3[size3+100];, if size3 is larger than the size of buff3, this will cause an out of bounds read from the stack, which could potentially lead to a crash or other undefined behavior.

7. Out of bound write (stack): In the line buff3[size3+100]='c';, if size3 is larger than the size of buff3, this will cause an out of bounds write to the stack, which could potentially lead to a crash or other undefined behavior.

8. Out of bound read (heap): In the line char OOBR_heap = buff4[100];, if buff4 is not large enough to hold an element at index 100, this will cause an out of bounds read from the heap, which could potentially lead to a crash or other undefined behavior.

9. Out of bound write (heap): In the line buff4[100]='c';, if buff4 is not large enough to hold an element at index 100, this will cause an out of bounds write to the heap, which could potentially lead to a crash or other undefined behavior.

10. Memory leak: In the lines if(size3>10){ buff4=0; }, if size3 is greater than 10, buff4 will be set to 0, but it is not freed. This could potentially lead to a memory leak, where memory is allocated but never freed.
