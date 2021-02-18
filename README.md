

<div align="center">
<h1>DEFIANT LIGHTING ENGINE
</div>
<div align="center">
<h4>for
</div>
<div align="center">
<h2>GameMakerStudio 2.3.1
</div>
<div align="center">
<h3>Author: Sedit ~2021
</div>
 
 <hr>
 <h2>UNDERSTANDING 3D WORLD SPACE AND HOW IT APPLIES TO OUR GMS ROOM </h2>   
 <hr>


    Every Shadowed object contains a variable called position which is type struct Vec3. Vec3 is just a container for an
  X, Y and Z World position variable as well as some useful functions for performing arithmetics and manipulation on the 3D vector.

    Positive Z is going down in the room swapping places with our objects vertical Y position and using the bottom of the objects 
  bounding box as reference to where exactly its base is in the world. 
  Obj.position.z = Obj.bbox_bottom;
  
  Y coordinate in the world is used to determine how high an object is off the ground IE: how far its bbox_bottom is from its 
  appearent location in our GMS room editor.

  Locations of objects in the world are calculated for lighting as such.

    The Horizantal X position in the world is calculated as the Left side of the bounding box plus half of the Objects Width which is 
  the difference between the Left and Right side of the Bounding box so,
  
  {
      Inst.object_width = Inst.bbox_right - Inst.bbox_left;
      Inst.position.x = Inst.bbox_left + (Inst.object_width * 0.5);
  }
  
  The Y position, also viewed as the Height from the ground is either Zero by default or defined by the user in an Instance variable
  in the room editor called Inst.world_height.
  
  {
      Inst.position.set(Inst.bbox_left + (Inst.object_width * 0.5),  Inst.world_height, Inst.bbox_bottom);
  }

      Two Utility functions are provided to simplify the conversion between these two coordinate systems however it is advised against 
  heavy use of these functions as they involve the returning of [X,Y] in worldspace_to_room's case and [X,Y,Z] in room_to_worldspace
  return. Despite it being nothing more than the creation of an array Profiling of this module has shown me that compared to all other
  stuff I have written here Array Creation and Return from these functions is a relatively resource intensive operation. This is
  not to say they should be totally avoided, just that it is best to first profile for yourself and decide how you feel about 
  the extra demand it puts on the system when you could easily just calculate it yourself as needed.
  
   worldspace_to_room(_vec)              ~  Provide it with a Vec3 position Vector and it will return a 2 element Array [X,Y]
   room_to_worldspace(_x, _y, _height)   ~  Provide it with X and Y room coordinates and a Height value and it returns a 3 component [X,Y,Z] Array
                                       which directly maps to the coordinate systems our Lights and Mapping algorithms use.
