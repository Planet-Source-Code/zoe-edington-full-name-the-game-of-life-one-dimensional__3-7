<div align="center">

## The Game Of Life\(one dimensional\)


</div>

### Description

This is a single dimensional Game of Life. The user inputs coordinates for living cells. Using the rules of the Game of Life the program determines if a cell will live or die. It will calculate for any amount of generations. It was developed in MS Visual C++ and uses an input file.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Zoe Edington \(full name\)](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/zoe-edington-full-name.md)
**Level**          |Unknown
**User Rating**    |3.0 (6 globes from 2 users)
**Compatibility**  |C, C\+\+ \(general\)
**Category**       |[Miscellaneous](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/miscellaneous__3-1.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/zoe-edington-full-name-the-game-of-life-one-dimensional__3-7/archive/master.zip)

### API Declarations

```
Additional Header Files:
Common.h:
#ifndef COMMON_H
#define COMMON_H
#include <stdio.h>
#include <stdlib.h>
typedef enum boolean { FALSE, TRUE } Boolean;
void Error(char *);
void Warning(char *);
 /* Error: report program error.
Pre: s points to the message to be printed.
Post: The function prints the message and terminates the program.
 */
void Error(char *s)
{
 fprintf(stderr, "%s\n", s);
 exit(1);
}
/*
Pre: s points to the message to be printed.
Post: The function prints the message.
 */
void Warning(char *s)
{
 fprintf(stderr, "%s\n", s);
}
#endif
Life.h
#define MAXCOL 60   /* maximum column range */
typedef enum state { DEAD, ALIVE } State;
typedef State Grid[MAXCOL+2];
FILE *inputs;
FILE *outputs;
void CopyMap(Grid map, Grid newmap);
void UserSaysYes(Grid map, Grid newmap);
void Initialize(Grid map);
int NeighborCount(Grid map, int column);
void WriteMap(Grid map);
void NextGeneration(Grid map,Grid newmap);
int BoundsMap(Grid map,int i);
```


### Source Code

```
/* Simulation of Conway's game of Life on a bounded grid in
one dimension.
Name: J. Edgington Date: 09/08/99
Pre: The user must supply an initial configuration of living cells.
Post: The program prints a sequence of maps showing the changes in
  the configuration of living cells according to the rules for the
  game of Life.
Uses: functions Initialize, WriteMap, NeighborCount, NextGeneration,
  CopyMap, and UserSaysYes
 */
#include "stdafx.h"
#include "common.h" /* common include files and definitions */
#include "life.h"  /* Life's defines, typedefs, and prototypes */
void main(void)
{
 int col;
 Grid map;  /* current generation */
 Grid newmap;  /* next generation  */
 if(( inputs = fopen( "input.dat", "r" ))== NULL )
 fprintf( outputs,"The file input.dat was not opened\n" );
 if(( outputs = fopen( "output.dat", "w" )) == NULL )
 fprintf( outputs,"The file ouput.dat. was not opened\n" );
 else
 {
 col=99;
 Initialize(map);
 WriteMap(map);
  fprintf(outputs,"[]This is the initial configuration you have chosen.[]");
	NextGeneration(map,newmap);
 CopyMap(map, newmap);
 WriteMap(map);
  fprintf(outputs,"[]Do you wish to continue viewing the new generations(y,n)?[]");
	}
 UserSaysYes(map, newmap);
 fclose( outputs );
	fclose( inputs );
}
/* NeighborCount: count neighbors of row,col.
Pre: The col is a valid cell in a Life configuration.
Post: The function returns the number of living neighbors of the living cell.
 */
int NeighborCount(Grid map, int col)
{
 int i=0; /* column of a neighbor of the cell (col) */
 int count = 0;  /* counter of living neighbors */
 for (i = col - 2; i <= col + 2; i++)
	if (BoundsMap(map,i) == ALIVE)
  count++;
 if (map[col] == ALIVE)
  count--;
 return count;
}
/*pre:index of array to look at. example:3
post:ALIVE or DEAD
 Also handles case of index < 1 or index > MAXCOL
(i.e. out of bounds)*/
int BoundsMap(Grid map,int i)
{
	if (i<1 || i>MAXCOL)
		return DEAD;
	else
		return map[i];
}
/* Initialize: initialize grid map.
Pre: None.
Post: All the cells in the grid map have been set to
 initial configuration of living cells.
 */
void Initialize(Grid map)
{
 int col=0; /* coordinates of a cell */
 fprintf(outputs,"[]This program is a simulation of the game of Life.[]"
 "[]The grid has a size of %d columns.[]", MAXCOL);
 fprintf(outputs,"[]On each line give an index for a living cell.[]"
	 "[]Terminate the list with the special index 0.[]");
 for (col = 0; col <= MAXCOL + 1; col++)
 {
 map[col] = DEAD; /* Set all cells empty, including the hedge. */
 }
 fscanf( inputs, "%d", &col );
 do
 {
 if ( col >= 1 && col <= MAXCOL)
  map[col] = ALIVE;
 else
	 fprintf(outputs,"[]Values are not within range.[]");
  fscanf(inputs,"%d", &col);
 }
 while ( col != 0); /* Checks for termination condition. */
}
/* WriteMap: display grid map.
Pre: The single line array map contains the current Life configuration.
Post: The current Life configuration is written for the user.
*/
void WriteMap(Grid map)
{
 int col;
 char a='*',d='-';
 for (col = 1; col <= MAXCOL; col++)
 {
	 if (map[col] == ALIVE)
 	fprintf(outputs,"%c",a);
  else
	 fprintf(outputs,"%c",d);
 }
}
/* CopyMap: copy newmap into map.
Pre: The grid newmap has the current Life configuration.
Post: The grid map has a copy of newmap.
 */
void CopyMap(Grid map, Grid newmap)
{
 int col;
 for (col = 0; col <= MAXCOL + 1; col++)
  map[col] = newmap[col];
}
/* UserSaysYes: TRUE if execution is to continue.
Pre: None.
Post: determines if user wants to get another generation of cells.
 */
void UserSaysYes(Grid map,Grid newmap)
{
 char c;
 do
 {
 fscanf(inputs, "%c", &c );
 if (c!= 10 )
	{
	if (c != 'n')
	 {
	 NextGeneration(map,newmap);
	 CopyMap(map, newmap);
	 WriteMap(map);
		fprintf(outputs,"Do you wish to continue viewing "
		"the new generations[]");
	}
	}
	else
	 c='y';
 }
 while (c == 'y' || c == 'Y');
}
/* NextGeneration
Pre: None
Post: Calculates the cells in the next generation.
*/
void NextGeneration(Grid map, Grid newmap)
{
 int col;
 for (col = 1; col <= MAXCOL; col++)
 switch(NeighborCount(map, col))
	{
 case 0:
 case 1:
	 newmap[col]= DEAD;
  break;
 case 2:
  newmap[col]= ALIVE;
  break;
 case 3:
	 if (map[col] == ALIVE)
	 newmap[col]=DEAD;
	 else
		newmap[col]= ALIVE;
	 break;
 case 4:
	 if (map[col] == ALIVE)
		newmap[col]=ALIVE;
	 else
		newmap[col]= DEAD;
  break;
 }
}
```

