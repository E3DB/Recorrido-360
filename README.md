A-Frame :: 360 VR tour
=================

> This is walkthrough guided experience using 360 photos and Aframe.
Project based on Medium's: [How to create a Virtual Tour using A-frame](https://medium.com/dschool/how-to-create-a-virtual-tour-using-a-frame-164941fea573)

## 🚀 Exploring The Scene


![Exploring the scene](https://cdn.glitch.com/5785f504-b035-426e-890b-7831a289e96c%2Fexploring-360scene-image.gif?v=1589300851617)

This code contains the basic components to explore & navigate a series of 360 images.
1. Put your VR headset [Goolge Cardboard] or drag your cursor to orbit around the scene. 
1. Stare a few seconds on the location icons, you will be teleported to a different 360 image.

## 📓 Our Project/Code

- README.md - project info/instructions
- index.html - this is our scene, click "Show" to see it
- assets - a folder with the images. [Check this out to learn how it works.](https://glitch.com/help/how-do-i/)
- LICENSE.md -  The MIT License

-----

## 💻 What the code does
Everything is happening in the index.html file. Check it out! 🔍


### Adding 360 images
Added the images to our assets so that we can load them whenever we select to move to different points in our Virtual environment. 
To be seamless, images should be equirectangular 360 images. 

![equrectangula image](https://cdn.glitch.com/5785f504-b035-426e-890b-7831a289e96c%2F49737463388_079f522dff_w.jpg?v=1589302412964)
 This is the example image.

If you dont have a 360 camera, you can downlaod them from:
* [Flickr](https://www.flickr.com/groups/equirectangular/). 
* [pixexid](https://pixexid.com/search/360%20panorama)

### Include the images in the scene
To add images to the scene:
* click on the assets link on the left side of glitch editor
* Click on each image and then copy url 
* Go back to **index.html** load the images in aframe.

```
<!-- here we load our 360 images and assign and id -->
   <img id="point1"  src="https://cdn.glitch.com/5785f504-b035-426e-890b-7831a289e96c%2FR0010125.JPG?v=1589137936652"/>
   <img id="point2"  src=""/>
   <img id="point3"  src=""/>
   <img id="point4"  src=""/>
        
```

### Skybox & set landing page 🚩
Pick the image that will be our landing space to one of the 360 images. 
So for that specify the id of the first image in src of **a-sky** tag as below. 
Notice “#” before the id. 
Many times people forget this and then 😭 that things are not working.

```
<!-- Define the starting image of the tour-->
<a-scene><a-sky id="skybox" src=""> </a-sky></a-scene>
```


### The Hotspot component
We are using a gaze interaction (learned in our previous lesson), where we’ll focus on the hotspot icon for 2 seconds and then the system will load 360 image attached to that hotspot.

> This is the code we use for loading the image attached to that hotspot
```
<script src="https://unpkg.com/aframe-look-at-component@0.5.1/dist/aframe-look-at-component.min.js"></script>
    <script>
      AFRAME.registerComponent('hotspots',{
        init:function(){
            this.el.addEventListener('reloadspots',function(evt){
            
            //get the entire current spot group and scale it to 0
            var currspotgroup=document.getElementById(evt.detail.currspots);
            currspotgroup.setAttribute("scale","0 0 0");
            
            //get the entire new spot group and scale it to 1
            var newspotgroup=document.getElementById(evt.detail.newspots);
            newspotgroup.setAttribute("scale","1 1 1");
          });
        }
      });
      AFRAME.registerComponent('spot',{
        schema:{
          linkto:{type:"string",default:""},
          spotgroup:{type:"string",default:""}
        },
        init:function(){
          
          //add image source of hotspot icon
          this.el.setAttribute("src","#hotspot");
          //make the icon look at the camera all the time
          this.el.setAttribute("look-at","#cam");
          
          var data=this.data;
          
          this.el.addEventListener('click',function(){
            //set the skybox source to the new image as per the spot
            var sky=document.getElementById("skybox");
            sky.setAttribute("src",data.linkto);
            
            var spotcomp=document.getElementById("spots");
            var currspots=this.parentElement.getAttribute("id");
            //create event for spots component to change the spots data
            spotcomp.emit('reloadspots',{newspots:data.spotgroup,currspots:currspots});
          });
        }
      });
    </script>
```
    
### Camera and cursor
We added the cmaera and the cursor entity to it. 
This will draw a little circular cursor, for example, in the code below, we created a ring-shaped cursor fixed to the center of the screen. To fix the cursor to the screen so the cursor is always present no matter where we look, we placed it as a child of the active camera entity. 
We pull it in front of the camera by placing it on the negative Z axis -1.8. 

```
<!-- Camera and cursor -->

<a-entity cursor="fuse:true;fuseTimeout:2000"
                  geometry="primitive:ring;radiusInner:0.01;radiusOuter:0.02"
                  position="0 0 -1.8"
                  material="shader:flat;color:#ff0000"
                  animation__mouseenter="property:scale;to:3 3 3;startEvents:mouseenter;endEvents:mouseleave;dir:reverse;dur:2000;loop:1">
</a-entity>
```     
This enables gaze-based interaction with hotspots. 
Notice fuseTimeout:2000. It specifies how long should the system wait before emitting click event on the entity on which gaze is focussed.

### Link 360 images to hotspots
The code has a group called hotspots and in it, there are subgroups of spots to be shown in each virtual spot. 

```
<!-- Link 360 images to hotspots -->
<a-entity id="spots" hotspots>
        <a-entity id="group-point1">
          <a-image spot="linkto:#point2;spotgroup:group-point2" position="-4 0 10"></a-image>
        </a-entity>
        <a-entity id="group-point2" scale="0 0 0">
          <a-image spot="linkto:#point1;spotgroup:group-point1" position="10 0 -8"></a-image>
          <a-image spot="linkto:#point3;spotgroup:group-point3" position="0 0 10"></a-image>
        </a-entity>
        <a-entity id="group-point3" scale="0 0 0">
          <a-image spot="linkto:#point2;spotgroup:group-point2" position="10 0 3"></a-image>
          <a-image spot="linkto:#point4;spotgroup:group-point4" position="-10 0 -3"></a-image>
        </a-entity>
        <a-entity id="group-point4" scale="0 0 0">
          <a-image spot="linkto:#point3;spotgroup:group-point3" position="0 0 -10"></a-image>
        </a-entity>
</a-entity>
```

----

## 💫 Remix me!

[Remix](https://glitch.com/~a-frame-360vr-tour) this to make your own 360 VR tour. 
Some fun ideas:
- [ ] Add 4 new 360 images to your assets [see details #Adding 360 images]
- [ ] Include the 4 images in your scene [index.html]
- [ ] Change the hotspot image (examples included in the assets)
- [ ] Adjust the position of the hotspots based on your images
- [ ] OPTIONAL Add 3D objects to your scene
- [ ] OPTIONAL Add some lighting and see what it looks like


## Share your url with the class!
