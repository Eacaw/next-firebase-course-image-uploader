## Next.js + Firebase Fireship.io Pro course - Image Uploader addon
---
### Disclaimer
> This was written trying to keep changes to the original repo to a minimum, and as such, this is written using Firebase V8, I have tried however to provide V9 examples in this readme where appropriate.

## About

A Simple addon to the pro next-firebase course provided to pro members on fireship.io. Many thanks to Jeff for the course, and his continued work educating us all. 

This repo adds a simple image upload module to the existing `Write Posts` page, updating the existing `ImageUploader.js` component and adding a new `UploadedImageSelector.js` component to display all images linked to a post as the user writes and offer a clean easy way to copy MD shortcode.

## How does it work?

At a very high level, there exists a new collection at the same level as the previous `hearts` collection stored within each `Post` document, `images`. Every time a user uploads an image using the pre-exisiting `ImageUploader` component, a new document is created in that post's `images` collection with an autoID and the image's `src`. The structure looks like this:

```
users   
└───UID
    └───posts
        └---PostOne
            ├---hearts 
            └---images
                └---UID
                    └---src  
 ```

 The `UploadedImageSelector` component then uses `useCollectionData` to fetch then map a column of images and provides a clickable thumbnail for each which will copy the Markdown shortcode the user can then paste directly into their post. 

 ## Changes made

## `admin/[slug].js`

Move `ImageUploader` from line 83 -> 50, placing it into the `<aside>` beneath the 'live view' button but above the delete button, pass in `postRef` prop. Also add the `UploadedImageSelector` component immediately below.

``` jsx
    <ImageUploader postRef={postRef} />
    <UploadedImageSelector postRef={postRef} />
 ```


 ## `ImageUploader.js`

 Use postRef prop to add a new document to the collection

> Firebase V8
 ``` javascript
   useEffect(() => {
    if (downloadURL) {
      postRef.collection('images').add({ src: downloadURL });
      // Reset the upload state
      setDownloadURL(null);
    }
  }, [downloadURL]);
  ```
> Firebase V9
``` javascript
  useEffect(() => {
    if (downloadURL) {
      const imageCollectionRef = collection(postRef, "images");
      addDoc(imageCollectionRef, { src: downloadURL });
      // Reset the upload state
      setDownloadURL(null);
    }
  }, [downloadURL]);
```
## `UploadedImageSelector.js`

This file is all new, but if you are using firebase V9, here are the changes that need to be made:

> Firebase V8
``` javascript
  const imagesCollection = postRef.collection('images');
```

``` javascript
useEffect(() => {
    if (coverImageSrc) {
        // Update the cover image on the post ref
        postRef.update({
        coverImage: coverImageSrc,
        });
    }
}, [coverImageSrc]);
  ```
> Firebase V9
``` javascript
import { collection, updateDoc } from "firebase/firestore";
```

``` javascript
const imagesCollection = collection(postRef, "images");
```

``` javascript
useEffect(() => {
    if (coverImageSrc) {
        // Update the cover image on the post ref
        updateDoc(postRef, {
            coverImage: coverImageSrc,
        });
    }
}, [coverImageSrc]);
```
## Possible futher work

- This could be adapted to suit a 'post images' side panel for posts in live view, instead of copying shortcode, opening up a modal or another tab to display a full/scaled resolution instance of the image.
- An option for the user to select which photo to use as the `coverPhoto` for the post.
- An option on each image to delete it from storage/post.