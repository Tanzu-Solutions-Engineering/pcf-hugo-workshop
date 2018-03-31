
Building the workshop site for a customer
---

We will use Hugo for this website.  
The code for workshop is available on github: [pcf-hugo-workshop](https://github.com/rjain-pivotal/pcf-hugo-workshop).

Five easy steps to create a custom workshop website in less than 10 minutes.
Use the power of Hugo + Wercker + PCF

# Dev Process

## Step 1: Create a branch per customer

When you create a branch, update the wercker.yml file on the branch to reflect the CF app name/route.

        ````
        box: debian
        build:
          steps:
            - arjen/hugo-build:
                version: 0.15
                theme: material-design
                flags: --destination=public --buildDrafts=false
        deploy:
          steps:
            - script:
                name: Install packages
                code: |
                  apt-get update --fix-missing
            - install-packages:
                packages: wget
            - joshuamckenty/cloud-foundry-deploy:
                api: $CF_API
                username: $CF_USER
                password: $CF_PASS
                organization: $CF_ORG
                space: $CF_SPACE
                appname: pcf-<customer>-workshop-blue
                alt_appname: pcf-<customer>-workshop-green
                domain: cfapps.io
                hostname: pcf-<customer>-workshop

        ````

You could create your own wercker app and target to PCF, or you could Update the wercker application https://app.wercker.com/#applications/56f1931f368950932900a530 to reflect the new branch and target deployment.
I can add you quickly as a collaborator on this project and you can create your own custom target (PWS or Internal PCF) For this, send me a note (rajesh.jain@pivotal.io) to add to the wercker project.

## Step 2: Add customer logo and update the config.toml

Get a customer logo and drop in the static/images folder
Update the config.toml

        ````
        baseurl = "http://pcf-<customer>-workshop.cfapps.io/"
        .....

        customer = "<customer>"
        ````


## Step 3: Update intro.md

Update the content\post\intro.md with your workshop details.

Date, Location, Agenda, Speakers and Prerequisites


## Step 4: Add Video/Slides

If you want to include youtube videos and slides (google or speakerdeck) add them as short-codes

See the example videos and slide includes
a. Onsi's circle of code
b. Josh Mckenty's PCF couple's therapy
c. Intro to PCF Slidedeck
d. Intro to PCF Google Slide

# Step 5: Commit the changes and kick back and relax

Commit the changes to git and wercker takes off the code and deploy's to PCF. And voila you are done.


# Learning Hugo and PCF Deployment process
In case you are interested in learning and testing the site locally here are the steps of how to install and configure Hugo. All this is automatically done by wercker above, so this is not required unless you want to learn hugo


## Step 1: Getting started with Hugo

Go to [https://github.com/spf13/hugo/releases](https://github.com/spf13/hugo/releases) and download Hugo for your operating system. If you are on Mac, the you can install using `brew` package manager as well.

```bash
$ brew update && brew install hugo
```

Once `hugo` is installed, make sure to run the `help` command to verify `hugo` installation. Below I am only showing part of the output of the `help` command for brevity.

```bash
$ hugo help
```

```
hugo is the main command, used to build your Hugo site.

Hugo is a Fast and Flexible Static Site Generator
built with love by spf13 and friends in Go.

Complete documentation is available at http://gohugo.io/.
```

You can check `hugo` version using the command shown below.

```bash
$ hugo version
```
```
Hugo Static Site Generator v0.15 BuildDate: 2015-11-26T11:59:00+05:30
```


## Step 2: Clone this Website to get the Workshop website

```bash
$ git clone https://github.com/rjain-pivotal/pcf-hugo-workshop
```

## Step 3: Build the website and test it locally
Hugo has inbuilt server that can serve content so that you can preview it. You can also use the inbuilt Hugo server in production as well. To serve content, execute the following command.

```bash
$ cd pcf-hugo-workshop
$ hugo server --buildDrafts
```
```
1 of 1 draft rendered
0 future content
1 pages created
0 paginator pages created
0 tags created
0 categories created
in 6 ms
Watching for changes in /Users/rjain/pcf-hugo-workshop/{data,content,layouts,static}
Serving pages from memory
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

## Step 4: Push it to cloud foundry

```bash
$cf push
```

## Step 5: Add content
Anytime you make changes in the content, delete the public directory, hugo will rebuild the directory.

Let's now add a post to our `workshop`. We will use the `hugo new` command to add a post.

```bash
$ hugo new post/lab-1.md
```
The above command will create a new directory `post` inside the `content` directory and create `lab-1.md` file inside it.

```bash
$ tree -a content
```
```
content
`-- post
    `-- lab-1.md

1 directory, 1 file
```

The content inside the `lab-1.md` looks like as shown below.

```
+++
date = "2016-02-14T16:11:58+05:30"
draft = true
title = "lab-1"

+++
```

The content inside `+++` is the TOML configuration for the post. This configuration is called **front matter**. It enables you to define about the post along with the content. Every post has three configuration properties shown above.

* **date** specifies the date and time at which post was created.
* **draft** specifies that post is not ready for publication yet so it will not be in the generated site
* **title** specifies title for the post

Let's add some content for **Lab-1** .

```
+++
date = "2016-02-14T16:11:58+05:30"
draft = true
title = "Lab-1 Content"

+++

```

You can add Categories, Tags and Weight. The theme material-design in this repo has been modified to add new partials for different type of content.

````
Categories = ["lab"]
Tags = ["cf","microservices","cloudfoundry"]
date = "2016-03-15T14:54:11-04:00"
title = "Lab 1: Build and Deploy Apps on PCF"
weight = 2

````

Example if the categories is lab, the index.html will use the themes\material-design\partials\labs.html

## Step 6: Make new partials and CSS

If you want to add new partials in Hugo theme, you and add them in the themes\<theme>\partials directory.

````
{{ $baseurl := .Site.BaseURL }}
<div class="col s6">
  <div class="card-panel hoverable blue-grey darken-1">
      <div class="card-content white-text">
  <!-- Card Content -->
      <h4><a href="{{ .Permalink }}">{{ .Title }}</a></h4>
      <p>{{ .Summary }}</p>
      <p>
      {{if .Params.tags }}
        {{ range $index, $tag := .Params.tags }}
          <a href="{{$baseurl}}/tags/{{ $tag | urlize }}/">#{{ $tag }}</a>
        {{ end }}
      {{end}}
      </p>
  </div>
</div>
</div>

````

If you want new colors for you cards, then you can refer to the Material Design CSS Color cards and change them

````
<div class="card-panel hoverable blue-grey darken-1">
    <div class="card-content white-text">

Or

<div class="card-panel hoverable blue-grey darken-1">
    <div class="card-content white-text">

````

You can also add completely new themes, but you will have to modify the config.toml, index.html, shortcodes, header.html to carry forward those changes.


## Step 7: Make posts public

New posts we have written are in draft status. To make a draft public, you can either run a command or manually change the draft status in the post to True.

```bash
$ hugo undraft content/post/lab-1.md
```

Now, you can start the server without `buildDrafts` option.

```
$ hugo server --theme=material-design
```

## Step 8: Generate website in public folder

To generate Hugo website code that you can use to deploy your website, type the following command.

```bash
$ hugo --theme=material-design
0 draft content
0 future content
5 pages created
2 paginator pages created
0 tags created
0 categories created
in 17 ms
```

After you run the hugo command, a public directory will be created with the generated website source.


## Step 8: Deploy to Cloud foundry

Create a Staticfile in public folder before pushing to Cloud Foundry.

```bash
$touch public/Staticfile
$cf push
```

Make sure you commit all your changes back to the git repo.

```bash
$ git add --all
$ git commit -am "New Content Added"
$ git push origin master
```

In couple of minutes, your website will be live https://pcf-hugo-workshop.cfapps.io

trigger changes
