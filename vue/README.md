![](https://img.shields.io/badge/Code-Vue-informational?style=flat&logo=vue.js&logoColor=white&color=2bbc8a)
![](https://img.shields.io/badge/Tools-Docker-informational?style=flat&logo=docker&logoColor=white&color=2bbc8a)


# Generate the image bulider

```
 # docker build -t <<builder-image-tag>> -f Dockerfile_builder .
```


# Create a new project

```
 # docker run -it -v ${PWD}:/root/ <<builder-image>> vue create <<new-project-name>>
```

# Forthrightly
```
 # docker run -it -v ${PWD}:/root/ <<builder-image>> vue create -df <<new-project-name>>
```


# References
https://youtu.be/qEHYCizwAKA
