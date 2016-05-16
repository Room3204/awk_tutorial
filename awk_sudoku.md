# 数独问题

就是用Awk解决数独问题，我数独玩的一般，重点还是看Awk的语法。只贴代码啦：
```
#!/usr/bin/awk -f
###############################################################################
#
# @(#) solve.awk - sudoku solver in awk using efficient backtracking algorithm
# @(#) $Id: solve.awk,v 1.16 2008/03/24 04:04:44 bduncan Exp bduncan $
# @(#) Copyright (C) 2005-2008, Bill Duncan, <bduncan-sudoku@beachnet.org>
#
# License:
#   This program is free software: you can redistribute it and/or modify
#   it under the terms of the GNU General Public License as published by
#   the Free Software Foundation, either version 3 of the License, or
#   (at your option) any later version.
#
#   This program is distributed in the hope that it will be useful,
#   but WITHOUT ANY WARRANTY; without even the implied warranty of
#   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#   GNU General Public License for more details.
#
#   You should have received a copy of the GNU General Public License
#   along with this program.  If not, see http://www.gnu.org/licenses/.
#
# Description:
#   - uses simple recursive backtracking algorithm with up-front and
#     ongoing elimination of invalid tries by tracking for each row,
#     column and region..
#
# Notes:
#   - precalc of regmap didn't seem to make a difference
#   - removed unmark() function and added a parameter to mark()
#
# Variables:
#   regmap[r,c]      ; pre-compiled, which region sector is r,c
#   master[r,c]      ; master matrix
#   C[col, elem]     ; true if elem in Column
#   R[row, elem]     ; true if elem in Row
#   Q[reg, elem]     ; true if elem in Region (Quadrant)
#
###############################################################################

BEGIN {
  SUBSEP    = ","    # so we can dump and it looks nice
  ORDER     = 9
  DEBUG     = 0
  count     = 0

  # precompile region map for faster lookup
# for (i = 0; i < ORDER; i++)
#   for (j = 0; j < ORDER; j++)
#     regmap[i+1,j+1]  =  int(i/3)*3+int(j/3)+1
}



function dump(   i,j) {
  printf "\n"
  for (i=1;i<=ORDER;i++) {
    if (!((i-1)%3)) printf "\n"
    for (j=1;j<=ORDER;j++) {
      if (!((j-1)%3)) printf " "
      printf " %1d",master[i,j]
    }
    printf "\n"
  }
  printf "\n"
}


function fregmap(r,c) {
# return regmap[r,c]
  return int((r-1)/3)*3+int((c-1)/3)+1
}


function inuse(r,c,try) {
  # q = fregmap(r,c)
  # can we use it or is it in use?  returns true if already used, not avail
  return (C[c,try] || R[r,try] || Q[fregmap(r,c),try])
}

function mark(r,c,try, flag,          q) {
  q = fregmap(r,c)
  Q[q,try]    = flag
  R[r,try]    = flag
  C[c,try]    = flag
  master[r,c] = flag ? try : 0
}


function search(r,c,   q,i,a,try) {
  # find the next empty slot from here r,c
  # if we've reached the end (no more empty) do check?
  # for each available number, recurse search
  count++

  while (master[r,c]) {
    if (++c >  ORDER) {
      c = 1
      if (++r >  ORDER) {
        # then we're done filling!  return goodness
        return 1
      }
    }
  }

  # for each of the available numbers for this slot
  for (try=1; try <= ORDER; try++) {
    if (! inuse(r,c,try)) {
      mark(r,c,try, 1)
      if (search(r,c)) return 1
    # else zero returned -- unwind
      mark(r,c,try, 0)  # unmark
    }
  }

  return 0
}


############
# PATTERNS #
############

NF == 0       { next }
$1 ~ /^#/     { next }

NF != ORDER       {
  printf "error on line %d, NF=%d\n", FNR, NF
  exit 1
}

{
  ++row
  for (col=1; col <= ORDER; col++) {
    mark(row,col,$col, 1)
  }
}


END {
  search(1,1)

  printf "\n# Searches=%d\n", count
  dump()
}
```