# The Shader File

The shader file is the program OpenGL uses to interpret the textures and colors that we want it to render.

- [The Vertex Shader](#the-fragment-shader)
- [The Fragment Shader](#the-fragment-shader)

## The Vertex Shader
```glsl
#type vertex
#version 330 core
```
type is used by the Jade engine to figure out what shader this is and
version is something, something.

```glsl
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec4 aColor;
layout (location = 2) in vec2 aTexCoord;
layout (location = 3) in float aTexId;
```

layout are variables taken in through the memory. OpenGL keeps them at
the denoted location and this is how we pull them from the memory and 
use them (see **ADD REFERENCE**).

```glsl
uniform mat4 uProjection;
uniform mat4 uView;
```

uniforms are variables we upload to the shader directly (see **ADD REFERENCE**).
Here they are the projection and the view, 2 matrices that will be used to adjust
how the "world" is perceived.

```glsl
out vec4 fColor;
out vec2 fTexCoords;
out float fTexId;
```

these variables (denoted ```out```) are variables that are passed from the vertex shader 
(the shader we are currently looking at) to the fragment shader. You can notice that these same
variables will also appear in the ```in``` section of the fragment shader.


```glsl
void main()
{
    fTexCoords = aTexCoord;
    fTexId = aTexId;
    fColor = aColor;
    gl_Position = uProjection * uView * vec4(aPos, 1.0);
}
```
This is the main function of the vertex shader. It defines the variables that the vertex
shader outputs to the fragment shader. <br>
```fTexCoords``` - The texture coordinates. <br>
```fTexId``` - The address in memory the texture refers to. <br>
```fColor``` - the color of the texture. <br>
```gl_Position``` - the placement of the "camera". It will not be passed to the fragment shader. <br>


## The Fragment Shader

```glsl
#type fragment
#version 330 core
```
tells the engine that we switched to the fragment shader.

```glsl
in vec4 fColor;
in vec2 fTexCoords;
in float fTexId;
```
These are the variables that we defined that we were outputting from the vertex shader
to the fragment shader.

```glsl
uniform sampler2D uTextures[8];
```
A variable that we upload to the shader ourselves (see **ADD REFERENCE**)

```glsl
out vec4 color;
```
The color OpenGL will use to render the texture

```glsl
void main()
{
    if (fTexId > 0){
        int id = int(fTexId);
        color = fColor * texture(uTextures[id], fTexCoords);
    } else {
       color = fColor;
    }
}
```
The point of this function is that as long as textures don't point to ID = 0, they are actual textures,
otherwise, we want to use a "static" color instead.