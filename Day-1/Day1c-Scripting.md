## Interacting with files from your hpc
Editing files on the hpc - nano  
navigating the prompt - ctrl+a/ctrl+e, tab to autocomplete, ctrl+c   
figuring out where you are - pwd 
what is in the directory you're in - ls  
creating directories and organizing files - mkdir, cd, mv, cp  
navigating to directories - space limitations and the scratch directory  
copying files from one location to another - recursive copying, rsync, cp, scp  
regex special characters - \* ,\t, \n, ., ~, ^, $  
uninterpreting special characters  
looking inside files - more, cat  
redirecting output - >   
grep - looking for patterns in your files  
sed - editing batches of files  
Downloading data from external sources (NCBI, ensembl, SRA, etc.) - rsync, curl, wget  
piping commands to link them together
grep - looking for patterns in your files  
sed - editing batches of files  
manipulating file contents - cut, paste  


## For & while loops 

Loops allow us repeat operations over a defined variable or set of files. Essentially, you need to tell Bash what you want to loop over, and what operation you want it to do to each item. 

Notice that the variable ***i*** set in the conditions for our loop is used to reference all the elements to be looped over in the operation using the term ***$i*** in this **for*** loop example: 
```bash 
# loop over numbers 1:10, printing them as we go
for i in {1..10}; do \
   echo "$i"; \
done
``` 


Alternatively, if you do not know how many times you might need to run a loop, using a ***while*** loop may be useful, as it will continue the loop until the boolean (logical) specified in the first line evaluates to `false`. An example would be looping over all of the files in your directory to perform a specific task. e.g. 
```bash
ls *.fastq.gz | while read x; do \
   # tell me what the shell is doing 
   echo $x is being processed...; 
   # provide an empty line for ease of viewing 
   yes '' | sed 1q;  \
   # unzip w/ zcat and print head of file
   zcat $x | head -n 4;  \
   # print 3 lines to for ease of viewing 
   yes '' | sed 3q ;
done
```

Perhaps we wanted to check how many reads contain the start codon `ATG`. We can do this by searching for matches and counting how many times it was found, and repeating this process for each sample using a for loop. 
```bash
ls *.fastq.gz | while read x; do \
   echo $x
   zcat $x | sed -n '2~4p' | head -4 | grep -o "ATG" | wc -l
done
```

We could use one of these loops to perform the nucleotide counting task that we performed on a single sample above. 
```bash
ls *.fastq.gz | while read x; do \
   yes '' | sed 1q 
   echo processing sample $x 
   zcat $x | sed -n '2~4p' | sed -n '1,10000p' | grep -o . | sort | grep 'C\|G' | uniq -c ;
done
```
## Scripting in bash 

So loops are pretty useful, but what if we wanted to make it even simpler to run. Maybe we even want to share the program we just wrote with other lab members so that they can execute it on their own FASTQ files. One way to do this would be to write this series of commands into a Bash script, that can be executed at the command line, passing the files you would like to be operated on to the script. 

To generate the script (suffix `.sh`) we could use the `nano` editor: 
```bash 
nano count_GC_content.sh
```

Add our program to the script, using a shebang `#!/bin/bash` at the top of our script to let the shell know this is a bash script. As in the loops we use the `$` to specify the input variable to the script. `$1` represents the variable that we want to be used in the first argument of the script. Here, we only need to provide the file name, so we only have 1 `$`, but if we wanted to create more variables to expand the functionality of our script, we would do this using `$2`, `$3`, etc. 
```bash 
#!/bin/bash
echo processing sample "$1"; zcat $1 | sed -n '2~4p' | sed -n '1,10000p' | grep -o . | sort | grep 'C\|G' | uniq -c
```

Now run the script, specifying the a FASTQ file as variable 1 (`$1`)
```bash
# have a quick look at our script 
cat count_GC_content.sh

# now run it with bash 
bash count_GC_content.sh SRR1039508_1.chr20.fastq.gz
```

Now we can use our while loop again to do this for all the FASTQs in our directory 
```bash
ls *.fastq.gz | while read x; do \
   bash count_GC_content.sh $x
done
```

What if we wanted to write the output into a file instead of printing to the screen? We could save the output to a *Standard output* (stout) file that we can look at, save to review later, and document our findings. The `1>>` redirects the output that would print to the screen to a file.
```bash
# create the text file you want to write to
touch stout.txt

# run the loop 
ls *.fastq.gz | while read x; do \
   bash count_GC_content.sh $x 1>> stout.txt 
done

# view the file 
cat stout.txt
```

These example programs run fairly quickly, but stringing together mutiple commands in a bash script is common and these programs take much longer to run. In these cases we might want to close our computer and go and do some other stuff while our program is running. We can do this using `nohup` which allows us to run a series of commands in the background, but disconnects the process from the shell you initally submit it through, so you are free to close this shell and the process will continue to run until completion. e.g. 
```bash
nohup bash count_GC_content.sh SRR1039508_1.chr20.fastq.gz &

# show the result 
cat nohup.out 
```

## R on the hpc 
submitting an R script
loading R libraries 
running R interactively 

## Large jobs on the hpc
building a command - reading the manual!!!   
writing a pbs script to submit a job  
checking the traffic on discovery- pbsmons  
submitting a job - mksub  
checking the status of a submitted job- qstat, myjobs   


## Error mitigation  
errors with job submission - exit status what does this tell you about what happened, look in your error/output file - does it look like you would expect?
stack exchange - what is it and how do yuo use it?  
Error messages, where can I find them and how do I know what they mean?  
Problem set  