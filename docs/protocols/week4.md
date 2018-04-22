# Week 4: Calling open reading frames with Prodigal, using BLAST, annotating genes with Interproscan

## Pre-lab notes

#### Step 1

Before we start this week, I want to encourage you all to start a computational lab notebook. This can take whatever form you want-- I often will just open a text document on my computer and save it on the cloud somewhere-- but it will be crucial that you keep a record of all the commands you run, especially for your project datasets. That way, if you get a funny result, or if you want to repeat an analysis, you can always go back to your lab notebook to see exactly what command you ran. I will sometimes include notes to myself about the analysis, or observations of the graphs I make. These notebooks are just for you-- I won't be checking them-- but now that we're about to get into the real analysis, I strongly recommend that you start one for your project datasets and maintain them throughout the rest of this course.


## Logging in to the remote server

#### Step 2
Boot as a Mac on the lab computer.

#### Step 3

Find and open the Terminal application (`Applications/Utilities`).

#### Step 4

This week, we're going to be using a different server called baross, which is my research server. This is because liverpool is actually a pretty small server-- it only has 10 processors. In contrast, baross has 96. Liverpool and baross share the same storage space, so all of your files on liverpool are mirrored on baross. This means that you should see the same file structure regardless of which server you log into, but the amount of computational power available to you will be different depending on which server you logged into. Please note: this is Rika's lab's research server, so please do NOT use this server unless instructed to.

Log on to the server using your Carleton username and password.
Command

`ssh [username]@baross.its.carleton.edu`

## Finishing up from last week

#### Step 5

Your assemblies from last week should have finished by now. If you haven't already, please use the `anvi-script-reformat-fasta` script on your assembled project datasets to make sure they're properly formatted and ready to go.

When that is done, please copy your newly formatted, assembled project assemblies and put them into the folder that we'll be sharing as a class, substituting the placeholder below with the name of your assembled file. This will be the shared folder for all of the data that you generate as a class.

```bash
cp [name of your formatted, assembled file] /usr/local/data/class_shared
```

## Searching for open reading frames

#### Step 6

First, we're going to learn how to identify open reading frames (ORFs) on your assembled toy dataset from last week. To do that, we’ll use a program called Prodigal. There are lots of programs that can identify ORFs, and there are strong distinctions between ORF callers for eukaryotes versus bacteria/archaea/viruses. Prodigal is one of the best ORF callers for microbial genomes and it is free, so that’s what we’re using today. If you find yourself wanting to identify ORFs on some sequence in the future, most ORF callers should work in a similar manner to Prodigal.

Go to your toy dataset directory:
Command

```bash
cd ~/toy_dataset_directory
```

#### Step 7

We're going to try out Prodigal on an assembled toy dataset to learn how this works. (Even though you assembled this toy dataset last week, we're going to use a subsample of that assmbled toy dataset in order to be more computationally efficient.) Copy toy_dataset_assembled_subsample.fa into your toy_assembly directory:
Command

```bash
cp /usr/local/data/toy_datasets/toy_dataset_assembly_subsample.fa toy_assembly
```

#### Step 8

Now that you're in the toy dataset directory, make a new directory and change into it.

```bash
mkdir ORF_finding
cd ORF_finding
```

#### Step 9

Now run Prodigal on your toy assembly subsample:

- The `-i` flag gives the input file, which is the assembly you just made.
- The `-o` flag gives the output file in Genbank format
- The `-a` flag gives the output file in fasta format
- The `-p` flag states which procedure you’re using: whether this is a single genome or a metagenome. This toy dataset is a single genome so we are using `–p` single, but for your project dataset, you will use `–p` meta.

```bash
prodigal -i ../toy_assembly/toy_dataset_assembly_subsample.fa -o toy_assembly_ORFs.gbk -a toy_assembly_ORFs.faa -p single
```

#### Step 10

You should see two files, one called `toy_assembly_ORFs.gbk` and one called `toy_assembly_ORFs.faa`. Use the program `less` to look at `toy_assembly_ORFs.faa`. You should see a fasta file with amino acid sequences. Each amino acid sequence is an open reading frame (ORF), or a putative gene that has been identified from your assembly.

```bash
less toy_assembly_ORFs.faa
```


## Finding the function of our open reading frames/genes/proteins

#### Step 11

Now we have all of these nice open reading frames, but we don’t know what their function is. So we have to compare them to gene databases. There are lots of them out there. First, we will use BLAST and compare against the National Center for Biotechnology Information (NCBI) non-redundant database of all proteins. This is a repository where biologists are required to submit their sequence data when they publish a paper. It is very comprehensive and well-curated.

Take a look at your Prodigal file again using the program less (see above). Copy the sequence of the first ORF in your file. You can include the first line that includes the > symbol.

#### Step 12

Navigate your browser to the BLAST suite at: https://blast.ncbi.nlm.nih.gov/Blast.cgi. Select Protein BLAST (blastp).

#### Step 13
Copy your sequence in to the box at the top of the page, and then scroll to the bottom and click “BLAST.” Give it a few minutes to think about it.

#### Step 14

While that's running, go back to the BLAST suite home page and try blasting your protein using `tblastn` instead of `blastp`.

What’s the difference?

`blastp` is a protein-protein blast. When you run it online like this, you are comparing your protein sequence against the National Centers for Biotechnology Information (NCBI) non-redundant protein database, which is a giant database of protein sequences that is “non-redundant”—that is, each protein should be represented only once.

In contrast, `tblastn` is a translated nucleotide blast. You are blasting your protein sequence against a translated nucleotide database. When you run it online like this, you are comparing your protein sequence against the NCBI non-redundant nucleotide database, which is a giant database of nucleotide sequnces, which can include whole genomes.

Isn't this a BLAST?!?
(Bioinformatics jokes! Not funny.)

#### Step 15

For your post-lab assignment, answer the following questions in a Word document that you will upload to the Moodle:

Check for understanding:

1. For both your blastp and tblastn results, list:
    - The top hit for each of your database searches
    - The e-value and percent identity of the top hit
    - What kind of protein it matched
    - What kind of organism it comes from

2. Take a look at the list of hits below the top hit.
    - Do they have the same function?
    - Do think you can confidently state what type of protein this gene encodes? Explain.
    - Describe the difference in your tblastn and blastp results, and explain in what scenarios you might choose to use one over the other.

#### Step 16

This works well for a few proteins, but it would be exhausting to do it for every single one of the thousands of proteins you will likely have in your dataset. We are going to use software called Interproscan to more efficiently and effectively annotate your proteins. It compares your open reading frames against several protein databases and looks for protein "signatures," or regions that are highly conserved among proteins, and uses that to annotate your open reading frames. It will do this for every single open reading frame in your dataset, if it can find a match.

Interproscan takes a long time to run. With a big dataset, it could run for hours. So let's open a screen session. (Note: for your toy dataset, this will take minutes, not hours, so screen isn't entirely necessary. But it's good to practice-- and for your project datasets, you will definitely want to be in screen.)

```bash
screen
```

#### Step 17

Interproscan doesn’t like all of the asterisks that Prodigal adds to the ends of its proteins. So get rid of them by typing this:

Explanation: `sed` is another Unix command. It is short for “stream editor” and it is handy for editing files on occasion, like when you’re trying to do a “find/replace” operation like you might do in Word. Here, we’re using sed to replace all of the asterisks with nothing.

```bash
sed 's/\*//g' toy_assembly_ORFs.faa > toy_assembly_ORFs.noasterisks.faa
```

#### Step 18

Now run interproscan.

- The `-i` flag gives the input file, which is your file with ORF sequences identified by Prodigal, with the asterisks removed.
- The `-f` flag tells Interproscan that the format you want is a tab-separated vars, or “tsv,” file.

```bash
interproscan.sh -i toy_assembly_ORFs.noasterisks.faa -f tsv
```

#### Step 19

When it's done, terminate your screen session.

(You could also try `Ctrl + A + k`  -- Hit "Control" and "a" at the same time, and then release and immediately press "k".)

```bash
exit
```

#### Step 20

Now let's take a look at the results. The output should be called `toy_assembly_ORFs.noasterisks.faa.tsv`. The output is a text file that's most easily visualized in a spreadsheet application like Excel. The easiest way to do that is to copy this file from the server to your own desktop and look at it with Excel. We're going to learn how to use FileZilla to do that. (Note, yes, you're connecting FileZilla to liverpool. Remember that baross and liverpool share the same file structure, so it shouldn't matter which one you connect to.)

Locate FileZilla in the Applications folder on your computer and open it. Enter the following:

1. For the Host: sftp://liverpool.its.carleton.edu
2. For the Username: your Carleton username
3. For the password: your Carleton password
4. For the Port: 22
5. Then click QuickConnect.

The left side shows files in the local computer you're using. The right side shows files on liverpool. On the left side, navigate to whatever location you want your files to go to on the local computer. On the right side, navigate to the directory where you put your Interproscan results. (`/Accounts/yourusername/toy_dataset_directory/ORF_finding/toy_assembly_ORFs.noasterisks.faa.tsv`) All you have to do is double-click on the file you want to transfer, and over it goes!

#### Step 21

Find your file on your local computer, and open your `.tsv` file in Excel. The column headers are in columns as follows, left to right:

1. Protein Accession (e.g. P51587)
2. Sequence MD5 digest (e.g. 14086411a2cdf1c4cba63020e1622579)
3. Sequence Length (e.g. 3418)
4. Analysis (e.g. database name-- Pfam / PRINTS / Gene3D)
5. Signature Accession Number (e.g. PF09103 / G3DSA:2.40.50.140)
6. Signature Description (e.g. BRCA2 repeat profile)
7. Start location
8. Stop location
9. Score - is the e-value of the match reported by member database method (e.g. 3.1E-52)
10. Status - is the status of the match (T: true)
11. Date - is the date of the run

#### Step 22

Answer the following question on the post-lab Word document you will submit to the Moodle:

Check for understanding:

4. Take a look at the annotation of the contig you BLASTed earlier. If there is more than one hit, that's because Interproscan is returning hits from multiple protein databases. What is the description of the hit with the best score, according to Interproscan? (Remember, Google can be a handy resource sometimes.) Does that match your BLAST results?
