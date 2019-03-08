// IN PROGRESS
// This is what I've worked out as a template for vertex shaders. Like you don't normally see shader info organized in this way, so I figured it's a good reference. I'll try to explain the point of things in comments

//The name and path. It's how Unity will identify this particular shader
Shader "Path/Name"
{
// All properties that can be passed into this shader
	Properties
	{
	// They're formatted like this:
	// _VariableName("Display Name", Type) = Default Value
		_RangedNumber("Ranged Number", Range(0.0, 3.0)) = 1.5
		_MainTex("Texture", 2D) = "white" {}
		_Color("Colour", Color) = (1,1,1,1)
		_Cube("Cube Map", Cube) = "" {}
	}
	//After properties, have the subshader
	SubShader
	{		
		//All tags are optional, often you'll have just RenderType and Queue
		Tags {
			// see here for more about RenderType https://docs.unity3d.com/Manual/SL-ShaderReplacement.html
			// Usually, you'll use just "Opaque" or "Transparent"
			"RenderType"="Opaque" 
			// Queue changes the default time when it's rendered. You can also set this in the material settings but this is default
			// possible values are Background, Transparent, Geometry, AlphaTest, Overlay and you can put a +1 or -1 to adjust from there
			"Queue" = "Transparent-1"
			// Other tags I don't use as much: 
			// "DisableBatching" I used this once when I had something that got broken when multiple meshes were batched together, and then I figured out a way to handle that.
			// "ForceNoShadowCasting"
			// "IgnoreProjector"
			// "CanUseSpriteAtlas"  Seems promising but I haven't used it
			// "PreviewType" 
		}
		
		//Blend says how it combines this with what was already rendered, if you're making it transparent
		// full list here: https://docs.unity3d.com/Manual/SL-Blend.html
		// Blend Off for opaque, but that's default
		// Blend SrcFactor DstFactor // where SrcFactor is what colour comes from this shader, and DstFactor is what colour was already on screen
		// So One One is add, 
		Blend One One
		
		//Zwrite and Ztest are about the ZBuffer, used for depth testing
		// Zwrite is On or Off, just to decide whether or not it writes its depth to the buffer
		ZWrite Off
		// ZTest has options of Less, Greater, LEqual, GEqual, Equal, NotEqual and Always
		// default is LEqual which means it renders this surface if it's distance from the camera is less or equal to what's already rendered
		ZTest LEqual
		// This offsets the ZWrite and ZTest, numbers are a slope multiplier and a distance offset, negative is closer, positive is further
		Offset 1, 1
		//Culling is for backface culling, default is Back but you can also use Front or Off
		Cull Off
		
		//you can specify certain shaders to be used for certain LODs
		LOD 100
		// A subshader can have multiple passes. Usually you only need one, but sometimes it's useful
		// I'm not getting into surface shaders right now, but if you were, you'd use that instead of everything inside Pass
		// Some of the stuff above, like Blend, ZWrite, Cull, you can also re-do those for each Pass and it will just do them all in sequence
		Pass
		{
		// this basically says use Cg/HLSL language for this pass
		// again, there are shaders that don't use this but I focused on just this language
			CGPROGRAM
			// We define the vertex and fragment functions we'll use
			// "vert" and "frag" are arbitrary names, and whatever we do there will need to be defined as a function further down
			#pragma vertex vert
			#pragma fragment frag
			// this imports a bunch of things that Unity provides. 
			// You can actually read it if you want to understand what some of the Unity, it'll be in your Unity installation
			//  under Editor/Data/CGIncludes  or you can see it online here: https://gist.github.com/hecomi/9580605
			#include "UnityCG.cginc"


			//closes the CGPROGRAM
			ENDCG
		}
	}
	// You can provide a fallback if for some reason this shader can't be used on this computer, it will use a different one
	// You provide the same Path/Name as the other shader has at the top
	FallBack "Path/OtherName"
}
