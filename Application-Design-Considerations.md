## Overview
While Trucker supports various runtimes and frameworks, it also demands that your application follows some basic design principles that make it compatible with a Cloud Application Platform such as Trucker. These design principles are essential for the consistency of your data, and the scalability of your application.

## Design Requirements

The primary concern is that you **do not store your data on local storage**. When you deploy an application on Trucker, the application runs within its own container, which is allocated a disk image to use. When the application is stopped, or if the application crashes, then the disk image is scrapped. When the application restarts again, it will use a new disk image. For this reason, **any changes you make to the local file-system will be lost upon restarting the application instance**.

While this appears to be a disadvantage, avoiding to use the local storage is really a necessity for scalable applications that need to maintain a consistent dataset across many worker instances. For this purpose, we reccommend that you use a blobstore service, like Amazon S3, a database service, or both!
