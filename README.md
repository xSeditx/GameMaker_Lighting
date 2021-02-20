

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


    Every Shadowed object contains a variable called position which is type struct Vec3. Vec3 is just a container for an X, Y and Z World position variable as well as some useful functions for performing arithmetics and manipulation on the 3D vector.

    Positive Z is going down in the room swapping places with our objects vertical Y position and using the bottom of the objects bounding box as reference to where exactly its base is in the world. *Obj.position.z = Obj.bbox_bottom;*
  
    Y coordinate in the world is used to determine how high an object is off the ground IE: how far its bbox_bottom is from its appearent location in our GMS room editor.

<hr>

  Locations of objects in the world are calculated for lighting as such.
<hr>

     The Horizantal X position in the world is calculated as the Left side of the bounding box plus half of the Objects Width which is the difference between the Left and Right side of the Bounding box so,
  
    {
        Inst.object_width = Inst.bbox_right - Inst.bbox_left;
        Inst.position.x = Inst.bbox_left + (Inst.object_width * 0.5);
    }
  
    The Y position, also viewed as the Height from the ground is either Zero by default or defined by the user in an Instance variable in the room editor called Inst.world_height.
  
    {
        Inst.position.set(Inst.bbox_left + (Inst.object_width * 0.5),  Inst.world_height, Inst.bbox_bottom);
    }

    Two Utility functions are provided to simplify the conversion between these two coordinate systems however it is advised against heavy use of these functions as they involve the returning of [X,Y] in worldspace_to_room's case and [X,Y,Z] in room_to_worldspace return. Despite it being nothing more than the creation of an array Profiling of this module has shown me that compared to all other stuff I have written here Array Creation and Return from these functions is a relatively resource intensive operation. This is  not to say they should be totally avoided, just that it is best to first profile for yourself and decide how you feel about the extra demand it puts on the system when you could easily just calculate it yourself as needed.
  
   worldspace_to_room(_vec)              ~ Provide it with a Vec3 position Vector and it will return a 2 element Array [X,Y] <br>
   room_to_worldspace(_x, _y, _height)   ~ Provide it with X and Y room coordinates and a Height value and it returns a 3 component [X,Y,Z] Array
                                       which directly maps to the coordinate systems our Lights and Mapping algorithms use.


























<hr>	
#### GLOBALS 
<hr>	
	 Type:ds_map()        -   shadow_sprite_map   Used to Generate a List of Shadows for every object depending on what Sprite the Object uses. 
	                                              Prevents overuse of Memory and Duplication of Shadow sprites
	
	 Type:array of arrays -   Falloff_rates       An Array of Arrays containing 3 Elements a piece. Intended to be used with Enum light_dist
	                                        Will return a 3 Component array containing the attenuation [Constant, Linear, Quadratic] values
											
											
											
<hr>

 #### TYPES:
<hr>	
        ENUMS
   ___________________________________________________________________________________________________________________________________________
        enum light_dist ~ Enumeration of build in, precalculated, attenuation rates for lights. 
		It gets used in the Global Array, Falloff_rates[], and has values ranging from
		light_dist._7px - light_dist._3250px measured in pixels
 
        EX:
		{
		    // If we wished the light to be almost completely fallen off by 325 pixels
		    obj_pointlight.light_falloff = Falloff_rates[light_dist._325px]
		}
		
		

<hr>

 #### STRUCTS
<hr>

   -------------------------------------------------------------------------------------------------------------------------------------------
     Vec3 (_x, _y, _z)  A Type used to Define a point in 3D Space. 
   ___________________________________________________________________________________________________________________________________________		
         X = Horizantal position in the room
         Y = Height of object off the ground. 
         Z = Vertical position in the room ( gms Y value)
			
        METHODS:
             Vec3.set(_x, _y, _z)       Sets the Values of XYZ
             Vec3.add(_other)           Adds _other Vec3 to Self and returns result in new Vec3 
             Vec3.subtract(_other)      Subtracts _other Vec3 from Self and return result in new Vec3
             Vec3.multiply(_other)      Multiplies _other Vec3 to Self and returns result in new Vec3 
             Vec3.divide(_other)        Divides Self by _other Vec3 and returns result in new Vec3 
             Vec3.length()              Returns the Length of the Vector from [0,0,0]
             Vec3.distance(_other)      Returns the Distance of _other from Self
   ____________________________________________________________________________________________________________________________________




<hr>

 #### CONSTANTS:
<hr>

   -------------------------------------------------------------------------------------------------------------------------------------------
        USED FOR INDEXING A SPECIFIC PART OF THE FALLOFF_RATES GLOBAL ARRAY.	
	        Constant_Term  = 0
            Linear_Term    = 1
            Quadratic_Term = 2

        EX:
		{
		    var LightFalloff = Falloff_rates[light_dist._600px];  
			var Linear_Value = LightFalloff[Linear_Term];
		}	
   ____________________________________________________________________________________________________________________________________			



<hr>	

 #### FUNCTION LIST:
<hr>
        function has_Normals(_objInst)                          Returns True if obj_shadowed Object has a Custom Normal Map
        function calculate_attenuation(_dist)                   Calculates Falloff rates for Linear and Quadratic Light fall off if provided a distance
        


Function Names |  Parameters   | Description
----------------- | --- | ----------------------------
room_to_worldspace |(_x, _y, _height)    |         Converts 2D XY roomspace, Plus additional Height variable into 3D world space for the Light engine
light_create | (_x, _y, _height, _radius, _color)|  Creates a Light with the desired properties
light_set_size | (_light, _radius)               | Sets the Size of the Light to a given Radius 
light_set_color | (_light, _color)               |   Sets the 32Bit Color of the Light object
light_set_color_rgb | (_light,  _r, _g, _b)      |  Sets the Color of Light using RGB Channels
light_set_position | (_light, _x, _y, _z)        |  Sets the Position of the Light in 3D WorldSpace
light_enable | (_light, _On_Off)                 |  Turns the Light On or Off using the boolean Parameter for True/False
is_On | (_light)                                 |  Returns True if Light is On
is_Off | (_light)                                |  Returns True if Light is Off
		
        [NOTE: DEPRECATED??? ]
		[Had uses but Changed the Lighting Algorithm eliminating need for Mask]
		  function light_set_mask(_light, _lightMask)-      
		  ---- Sets the Mask a light will use to Blend to the Surface		
		  
        [draw_surface_pos Still works but is also no longer needed]
          function draw_surface_pos(_surfID,  _x1, _y1, _x2, _y2, _x3, _y3, _x4, _y4, _alpha)	
		  ---- Surface equivalent to draw_sprite_pos(...)	
		  
		  
  ##### DEBUG ONLY FUNCTIONS:
	
        function _WARN_ME(_text) - This ensures as projects grow larger I remember details	
		    Text to Remind the Programmer of whatever _text says printed to screen
		    Generates a Debug Message and Break when in _DEBUG mode. 
=========================================================================================================================================== 


