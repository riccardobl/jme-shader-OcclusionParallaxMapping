# OcclusionParallaxMapping for jMonkeyEngine

Usage.


```glsl

//Fragment shader

// Define which channel of the texture is used to store height data (rgba)
#define HEIGHT_MAP R_COMPONENT 
// #define HEIGHT_MAP G_COMPONENT 
// #define HEIGHT_MAP B_COMPONENT    
// #define HEIGHT_MAP A_COMPONENT 
// If the texture contains DEPTH, define the channel using DEPTH_MAP instead of HEIGHT_MAP

// Override texture sampling function
// #define Texture_sample myTextureSample

// Import OcclusionParallax
#import "Shaders/OcclusionParallax.glsllib"

uniform vec3 g_CameraPosition; // camera position in world space
uniform sampler2D m_ParallaxMap; // gray scale height/depth map

in vec3 WPos; // fragment position in world space
in vec3 WNorm; // fragment normal in world space
in vec3 WTang; // fragment tangent in world space
in vec2 TexCoord;

out vec4 outFragColor;
//....
void main(){
  //...
  
  mat3 tbnMat = mat3(WTang.xyz, WTang.w * cross(wNorm, WTang.xyz), wNorm);        
  vec3 viewDir = normalize(g_CameraPosition - WPos);
  vec3 vViewDir =  viewDir * tbnMat;  

  float m_ParallaxHeight=0.3;
  Parallax_initFor(vViewDir,m_ParallaxHeight);
  
  //...
  
  vec2 newTexCoord=TexCoord; // Coordinates to be displaced by the parallax
  Parallax_displaceCoords(newTexCoord,m_ParallaxMap);

  //...
  
  // Displace other coordinates, no need to call Parallax_initFor again.
  // vec2 newTexCoord2=TexCoord2; // Coordinates to be displaced by the parallax
  // Parallax_displaceCoords(newTexCoord2,m_ParallaxMap);
  
  //...

  outFragColor=texture(m_DiffuseMap,newTexCoord);
}
```


Example implementation in PBRLighting
https://github.com/riccardobl/jme-shader-OcclusionParallaxMapping/tree/master/Materials/OcclusionParallax
