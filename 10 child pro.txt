#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
int main() 
{
	
	int total_Sum = 0;

	int numbers[1000];                    //array declaration of 1000 numbers
	int j_pipe[1];
	int sum = 0;
	int i;
	int j;
	
	for (i = 0; i < 1000; i++)		//populating array with 1 to 1000 numbers
	{
		numbers[i] = i+1;
	}

	for (j = 0; j < 1000; j++)
	{	
		if(j % 100 == 0)    //check by which every child get 100 numbers for adding
		{
			pipe (j_pipe);       //pipe
			sum = 0;

			int cpid = fork();    //creating child process using fork
			
			if (cpid == 0)              // Child starts when cpid is equal to 0
			{
				
				close(j_pipe[0]);
				
				for (i = j; i < j+100; i++)
				{
					sum = sum + numbers[i];             //sum computing by child
				}
				write(j_pipe[1], &sum, sizeof(sum)); //writing into pipe
				
				
				close(j_pipe[1]);
				
				kill(getpid(), SIGKILL);    //child process killing after its work
			}
			else 
			{
				close(j_pipe[1]);
				
				wait(NULL);
				
				read (j_pipe[0], &sum, sizeof(sum));		//readong from the pipe
				total_Sum = total_Sum + sum;
				
				close(j_pipe[0]);
			}
		}
	}
	printf("Total Sum = %d   ", total_Sum);     //total sum printing
	return 0;
}