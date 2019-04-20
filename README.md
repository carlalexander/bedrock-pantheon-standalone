# Bedrock template project for Pantheon (standalone)

This is a template project for using [bedrock][1] with [Pantheon][2]. This 
project shows you how to use bedrock with Pantheon without the use of any 
external services for code deployment. You can either SFTP or more preferably 
`git push` your changes directly to Pantheon.

## Creating a project

To create a new project, just use the following command:

```console
$ composer create-project carlalexander/bedrock-pantheon-standalone
```

You'll then need to update the environment variables in your project's `.env` 
file. You can read about all the Bedrock environment variables in the [Bedrock 
documentation][3].

## Important changes

There are a few important changes from a regular bedrock project. You can find 
them explained below.

### You must commit the vendor and web directories

This is the largest change from using a standard bedrock project. Pantheon 
doesn't support composer for installing dependencies when deploying to their 
servers. This means that you will need to commit the entire `vendor` and `web`
directories into your git repo. You can see the original commit doing that 
[here][4].

### Symlink the uploads directory

This is another complicated change. Pantheon doesn't store media files inside 
the `uploads` directory, but inside another directory outside the project root 
called `files`. For media uploads to work on Pantheon, we need the 
`web/app/uploads` directory to point to that `files` directory. 

To fix this issue, we need to convert the `web/app/uploads` directory to a 
symlink. That symlink will point the `web/app/uploads` directory to the `files` 
directory. This is already done for you in the project in [this commit][5].
But this means that you will need to recreate that `files` directory outside 
your project from uploads to work locally.

### Location of the .env file

Similar to the issue with the `uploads` directory, you will need to store your 
`.env` outside the project root on the Pantheon server. The bedrock application 
configuration loader [was updated][6] to look for the `.env` file in the 
`files/private` directory where Pantheon stores all [files that shouldn't be
accessible to the public][7]. 

### Creating the initial .env file

Unlike a standard WordPress site, a Bedrock site uses environment variables to
manage sensitive credentials. These environment variables can come from 
different sources. The most important one being `.env` file which Bedrock uses 
instead of the standard WordPress `wp-config.php` file.

Pantheon won't create the initial `.env` file that your Bedrock site needs. 
You're going to have to create it yourself and upload it to the Pantheon server.
To easiest way to do that is by connecting to your Pantheon server using FTP and
creating the `.env` file.

![Connection Info][8]

To connect to your Pantheon site using FTP, go to your site admin panel in the
Pantheon dashboard. There, you'll see a **Connection Info** button to the right
as shown above. This will open a menu with all the credentials used to connect
to your Pantheon site.

![SFTP credentials][9]

You'll find the SFTP credentials at the bottom. Use these SFTP credentials to 
connect to your Pantheon site. Once connected, you want to go to the `files`
directory and create the `private` directory. You want to create your `.env` 
file in the `private` directory that you just created with the following:

```
WP_ENV=development
WP_SITEURL=${WP_HOME}/wp

# Generate your keys here: https://roots.io/salts.html
AUTH_KEY='generateme'
SECURE_AUTH_KEY='generateme'
LOGGED_IN_KEY='generateme'
NONCE_KEY='generateme'
AUTH_SALT='generateme'
SECURE_AUTH_SALT='generateme'
LOGGED_IN_SALT='generateme'
NONCE_SALT='generateme'
```

It's important that you replace all the keys with new ones that were generated 
[here][10]. You'll also notice that this is a more trimmed down `.env` file than 
what you're used to see with Bedrock. That's because Pantheon supplies a lot of 
the environment variables that we'd store in the `.env` file normally.

## Acknowledgements

Thanks to the [Roots team][11] for creating and maintaining the Bedrock project. 
Also thanks to [Andrew Taylor][12] for his [repo][13] showing how to have 
advanced deployment workflow with WordPress and Pantheon.

[1]: https://roots.io/bedrock
[2]: https://pantheon.io
[3]: https://roots.io/bedrock/docs/installing-bedrock
[4]: https://github.com/carlalexander/bedrock-pantheon-standalone/commit/553f874086f72b6ea7da1490fea607c5acf7a8d4
[5]: https://github.com/carlalexander/bedrock-pantheon-standalone/commit/297d2a43fb330036aff3bd02e14740c6b7ad1c85
[6]: https://github.com/carlalexander/bedrock-pantheon-standalone/70ba9d4e5100fb7fd95e1b6976bd8679887cb339
[7]: https://pantheon.io/docs/private-paths/#private-path-for-files
[8]: https://d.pr/i/RnWLIb+
[9]: https://d.pr/i/1sbaUa+
[10]: https://roots.io/salts.html
[11]: https://roots.io
[12]: http://www.ataylor.me/
[13]: https://github.com/ataylorme/Advanced-WordPress-on-Pantheon
