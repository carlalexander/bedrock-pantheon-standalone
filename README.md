# Bedrock template project for Pantheon (standalone)

This is a template project for using [bedrock](https://roots.io/bedrock/) with [Pantheon](https://pantheon.io/). This 
project shows you how to use bedrock with Pantheon without the use of any external services for code deployment. You
can either SFTP or more preferably `git push` your changes directly to Pantheon.

## Versions used

This template project was built using Bedrock 1.10.2.

## Important changes

There are a few important changes from a regular bedrock project. You can find them explained below.

### You must commit the vendor and web directories

This is the largest change from using a standard bedrock project. Pantheon doesn't support composer for installing 
dependencies when deploying to their servers. This means that you will need to commit the entire `vendor` and `web`
directories into your git repo. You can see the original commit doing that [here](https://github.com/carlalexander/pantheon-bedrock/commit/553f874086f72b6ea7da1490fea607c5acf7a8d4).

### Symlink the uploads directory

This is another complicated change. Pantheon doesn't store media files inside the `uploads` directory, but inside 
another directory outside the project root called `files`. For media uploads to work on Pantheon, we need the 
`web/app/uploads` directory to point to that `files` directory. 

To fix this issue, we need to convert the `web/app/uploads` directory to a symlink. That symlink will point the 
`web/app/uploads` directory to the `files` directory. This is already done for you in the project in [this commit](https://github.com/carlalexander/pantheon-bedrock/commit/297d2a43fb330036aff3bd02e14740c6b7ad1c85).
But this means that you will need to recreate that `files` directory outside your project from uploads to work locally.

### Location of the .env file

Similar to the issue with the `uploads` directory, you will need to store your `.env` outside the project root on the
Pantheon server. The bedrock application configuration loader [was updated](https://github.com/carlalexander/pantheon-bedrock/commit/70ba9d4e5100fb7fd95e1b6976bd8679887cb339)
to look for the `.env` file in the `files` directory where Pantheon stores all your uploaded files. Due to the custom 
nature of that setup, you will need to manage the `.env` file via SFTP.
