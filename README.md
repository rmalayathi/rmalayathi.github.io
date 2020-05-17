## TASK 2 - DEVOPS

You can use the [editor on GitHub](https://github.com/rmalayathi/rmalayathi.github.io/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/rmalayathi/rmalayathi.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.


Problem Statement :
1. Create a container image that’s has Jenkins installed using Dockerfile. 

2. When we launch this image, it should automatically start the Jenkins service in the container.

3. Create a job chain of job1, job2, job3 and job4 using the build pipeline plugin in Jenkins.

4. Job1: Pull the GitHub repo automatically when some developers push the repo to GitHub.

5. Job2: By looking at the code or program file, Jenkins should automatically start the respective language interpreter install image container to deploy code ( eg. If code is of PHP, then Jenkins should start the container that has PHP already installed ).

6. Job3: Test your app if it is working or not.

7. Job4: If the app is not working, then send an email to the developer with error messages.

8. Create One extra job job5 for monitor: If the container where the app is running. fails due to any reason then this job should automatically start the container again.

SOLUTION :
So we will start by creating a Dockerfile using centos: latest image pulled from docker hub.

No alt text provided for this image
FROM: the image to be used for container

RUN: commands to be executed while building the modified container

We will be installing wget for downloading the files from url, and before moving further we will set up the yum repository for Jenkins from URLs using wget command. Then we will first install java as it is a prerequisite for the smooth functioning of Jenkins. We will make a Jenkins entry in the sudoers file to give it root powers without the requirement for any password. We will also require Git installed to be used in Jenkins as a plugin. Python3 is installed for a mailing code that will be used later in a job.

**The email part can be done directly from Jenkins plugins as well. I have used python here. If anyone needs the Jenkins method, I will make a separate article for it.

COPY: command will be used to copy files from host to image while building it.

CMD: the cmd used here will keep the Jenkins live till the container is on and will start on container boot.

and the mail.py file :

No alt text provided for this image
To build the image we will use: **. is to be replaced with the path of Dockerfile.

docker build -t jenkin:latest .
On successful build, you will see a screen like this.

No alt text provided for this image
Now we will run our container.

docker run -it --privileged -p 9999:8080 -v /:/host jenkin:latest

--privileged has been used for special access to be used later while playing with dockers.

-v: the mount seen here has been done for the same as well.

No alt text provided for this image
No alt text provided for this image
We will also set up ngrok on host os as the port is already mapped so that webhooks can be configured. (Please refer my previous article for any help.




Now we will be moving on to our jobs.

First job :

No alt text provided for this image
No alt text provided for this image
No alt text provided for this image
I forgot to mount another point for sharing files for deployment so I am copying files to host os.

No alt text provided for this image
Moving on to next job,

No alt text provided for this image
This will be triggered as soon as Job1 completes and will deploy a container on the host system.

**I have put one condition of normal HTML server, conditions can be added accordingly added as per the extensions and container images can be run on those cases.

Job 3 is to test our application.

No alt text provided for this image
Job3 will be successfully built if the test fails so it can go to Job4 and mail to dev.

Job4 :

No alt text provided for this image
Now time for the last one, the monitoring job. This is set to check every minute for the status of the container.

No alt text provided for this image
No alt text provided for this image
and the build pipeline looks as :

No alt text provided for this image
**For the confusion about docker commands, those commands actually run on host os using a small trick. I used chroot to switch the root to that of host os and run all the commands their and after running the required commands I switched back to the container.

For any queries, issue or suggestion, please feel free to contact me anytime. I will respond as soon as possible.
