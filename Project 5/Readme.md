# Introduction to shell Scripting and User Input.


## My First Shell Script

**Step 1**: on the terminal, i open a folder called shell-scripting usind the command `mkdir shell-scripting`. This will hold all the script we will write in this lesson,

**Step 2**: create a file called user-input.sh using the command `touch user-input.sh` 

**Step 3**: Inside the file copy and paste the block of code below:

`#!/bin/bash

#Prompt the user for their name
echo "Enter your name:"
read name

#Display a greeting with the entered name
echo "Hello, $name! Nice to meet you."`


**Step 4**: save your file

**Step 5**: run the command `sudo chmod +x user-input.sh` this makes the file executable.

**Step 6**: run the script using the command ./user-input.sh

![Alt text](<Images/schell script 2.png>)



## Directory Manipulation and Navigation

  We will be writing a simple shell script as a way of practicing what we learnt.

  This script will display the current directory, create a new directory called "my_directory".Change to that directory, create two files inside it, list the files, move back one level up, remove the "my_directory" and its contents, and finally lists the files in the current directory again.

Let us get started below:

**Step 1**: open a file named navigating-linux-filesystem.sh

**Step 2**: paste the code block into your file.

`
#!/bin/bash

#Display current directory
echo "Current directory: $PWD"

#Create a new directory
echo "Creating a new directory..."
mkdir my_directory
echo "New directory created."

#Change to the new directory
echo "Changing to the new directory..."
cd my_directory
echo "Current directory: $PWD"

#Create some files
echo "Creating files..."
touch file1.txt
touch file2.txt
echo "Files created."

#List the files in the current directory
echo "Files in the current directory:"
ls

#Move one level up
echo "Moving one level up..."
cd ..
echo "Current directory: $PWD"

#Remove the new directory and its contents
echo "Removing the new directory..."
rm -rf my_directory
echo "Directory removed."

#List the files in the current directory again
echo "Files in the current directory:"
ls`


**Step 3**: `sudo chmod +x navigating-linux-filesystem.sh` to set execute permission on this file.

**Step 4**: run your script using this command ./navigating-linux-filesystem.sh

![Alt text](<Images/Directory Manipulation and navigation.png>)



## File Operations and Sorting

We will be writing a simple shell script that focuses on File Operation and sorting.

This script creates files (file1,txt file2.txt file3.txt), displays the files in thier current order, sorts them alphabetically, saves the sorted files in sorted_files.txt, displays the sorted files, removes the original files, renames the sorted file to sorted_files_sorted_alphabetically.txt, and finally displays the contents of the final sorted file.

Lets start below:

**Step 1**: Opened terminal and created a file called sorting.sh using the command `touch sorting.sh`

**Step 2**: Copied and pasted the code block below into the file

`
#!/bin/bash

#Create three files
echo "Creating files..."
echo "This is file3." > file3.txt
echo "This is file1." > file1.txt
echo "This is file2." > file2.txt
echo "Files created."

#Display the files in their current order
echo "Files in their current order:"
ls

#Sort the files alphabetically
echo "Sorting files alphabetically..."
ls | sort > sorted_files.txt
echo "Files sorted."

#Display the sorted files
echo "Sorted files:"
cat sorted_files.txt

#Remove the original files
echo "Removing original files..."
rm file1.txt file2.txt file3.txt
echo "Original files removed."

#Rename the sorted file to a more descriptive name
echo "Renaming sorted file..."
mv sorted_files.txt sorted_files_sorted_alphabetically.txt
echo "File renamed."

#Display the final sorted file
echo "Final sorted file:"
cat sorted_files_sorted_alphabetically.txt`


**Step 3**: Set execute permission on sorting.sh using this command `sudo chmode +x sorting.sh`

**Step 4**: Run the script using the command `./sorting.sh`

![Alt text](<Images/Files operation and sorting.png>)



## WOrking with Numbers and Calculations

This script defines two variables num1 and num2 with numeric values, performs basic arithemtic operations(additionsubstraction,multiplication,division and modulus) and displays the results, it also performs more complex calculations such as raising the num1 to the power of 2 and calculating the square root of num2, and displays those results as well.

Lets run this steps below:

**Step 1**: on the terminal create a file called calculations.sh using the `touch calculations.sh`

**Step 2**: Paste the code block:

`#!/bin/bash

#Define two variables with numeric values
num1=10
num2=5

#Perform basic arithmetic operations
sum=$((num1 + num2))
difference=$((num1 - num2))
product=$((num1 * num2))
quotient=$((num1 / num2))
remainder=$((num1 % num2))

#Display the results
echo "Number 1: $num1"
echo "Number 2: $num2"
echo "Sum: $sum"
echo "Difference: $difference"
echo "Product: $product"
echo "Quotient: $quotient"
echo "Remainder: $remainder"

#Perform some more complex calculations
power_of_2=$((num1 ** 2))
square_root=$(awk "BEGIN{ sqrt=$num2; print sqrt }")

#Display the results
echo "Number 1 raised to the power of 2: $power_of_2"
echo "Square root of number 2: $square_root"`


**Step 3**: Set execute permission on calculations.sh using the command: `sudo chmod +x calculations.sh`

**Step 4**: run the script using this command `./calculations.sh`

![Alt text](<Images/Working with numbers and calculations.png>)



## File Backup and Timestamping

This shell scripting example is focused on file backup and timestamp.

This script defines the source directory and backup directory paths. It then Creates a time stamping using the current current date and time, and creates a backup directory with the timestamp appended to its name.

**Step 1**: On the terminal i opened a file backup.sh using a command `touch backup.sh`

**Step 2**: Copied and pasted the code block below into the file.

#!/bin/bash

#Define the source directory and backup directory
source_dir="Home/Desktop/AgbaDev/shell"
backup_dir="Home/Desktop/AgbaDev"

#Create a timestamp with the current date and time
timestamp=$(date +"%Y%m%d%H%M%S")

#Create a backup directory with the timestamp
backup_dir_with_timestamp="$backup_dir/backup_$timestamp"

#Create the backup directory
mkdir -p "$backup_dir_with_timestamp"

#Copy all files from the source directory to the backup directory


**Step 3**: Set execute permission on backup.sh using this command ` sudo chmod +x backup.sh`

**Step 4**: Run your script using the command `./backup.sh`

![Alt text](<Images/file backup.png>)