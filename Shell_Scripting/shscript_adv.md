# Conditionals

Conditionals (if statements) allow scripts to execute commands only when specific conditions are met.

**Basic if-then-else syntax:**
```bash
if [ condition ]
then
    # statements if condition is true
else
    # statements if condition is false
fi
```
- Always put spaces around the condition inside `[ ]`.
- Every `if` must end with `fi`.
- The `else` block is optional, but recommended for clarity.

**Example:**
```bash
if [[ $# == 2 ]]
then
  echo "number of arguments is equal to 2"
else
  echo "number of arguments is not equal to 2"
fi
```

**String comparison example:**
```bash
string_var="Yes"
[ "$string_var" == "Yes" ]
```

**Multiple conditions:**
```bash
if [ condition1 ] && [ condition2 ]
then
    echo "Both conditions are true"
else
    echo "One or both conditions are false"
fi

if [ condition1 ] || [ condition2 ]
then
    echo "At least one condition is true"
else
    echo "Both conditions are false"
fi
```

# Logical Operators

- `==` : equal to  
  Example: `[ $a == 2 ]`
- `!=` : not equal to  
  Example: `[ $a != 2 ]`
- `!` : logical negation  
- `-le` : less than or equal to  
  Example:
  ```bash
  a=1
  b=2
  if [ $a -le $b ]
  then
     echo "a is less than or equal to b"
  else
     echo "a is not less than or equal to b"
  fi
  ```

# Arithmetic Calculation

- Inline calculation:
  ```bash
  echo $((3+2))
  ```
- Using variables:
  ```bash
  a=3
  b=2
  c=$(($a+$b))
  echo $c
  ```
- Bash only supports integer arithmetic (no floating point):
  ```bash
  echo $((3/2))  # Outputs 1
  ```

# Arrays

- Declare an array:
  ```bash
  my_array=(1 2 "three" "four" 5)
  ```
- Create an empty array:
  ```bash
  declare -a empty_array
  ```
- Append elements:
  ```bash
  my_array+=("six")
  my_array+=(7)
  ```
- Access elements:
  ```bash
  echo ${my_array[0]}    # First element
  echo ${my_array[2]}    # Third element
  echo ${my_array[@]}    # All elements
  ```

# Loops

- Basic for loop:
  ```bash
  for (( i=0; i<=N; i++ )) ; do
    echo $i
  done
  ```
- Example: print numbers 0 to 6
  ```bash
  N=6
  for (( i=0; i<=$N; i++ )) ; do
    echo $i
  done
  ```
- Looping through arrays and summing elements:
  ```bash
  #!/usr/bin/env bash
  my_array=(1 2 3)
  count=0
  sum=0
  for i in ${!my_array[@]}; do
    echo ${my_array[$i]}
    count=$(($count+1))
    sum=$(($sum+${my_array[$i]}))
  done
  echo $count
  echo $sum
  ```
