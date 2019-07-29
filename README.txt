Documentation Author: Niko Procopi 2019

This tutorial was designed for Visual Studio 2017 / 2019
If the solution does not compile, retarget the solution
to a different version of the Windows SDK. If you do not
have any version of the Windows SDK, it can be installed
from the Visual Studio Installer Tool

Welcome to the Bufferless Rendering Tutorial!
Prerequesites: Intro Graphics

This tutorial can be very useful while learning Sprite Batching,
and Deferred Rendering. Bufferless rendering allows graphics to
be rendered, without using a vertex buffer or an index buffer.

In this tutorial, shaders are in the main.cpp file. The Vertex 
Shader is located at line 61, and the Fragment Shader is located
at line 83. Using shaders in CPP files, or their own GLSL files,
is 100% a matter of preference.

To render without buffers, we compile the shaders into a shader
program, we call glUseProgram to activate the program, and then
we call glDrawArrays(GL_TRIANGLE_STRIP, 0, 4); to draw 4 vertices.

If you run the program, this gives us a square with a design,
and you may be wondering how this works without a vertex buffer
or index buffer. Here is how:

When the Vertex Shader runs, it can tell which vertex it is 
currently processing. The variable gl_VertexID is built-into GLSL, 
and it tell us which vertex is being processed.
Because we draw 4 vertices, that means  the vertex shader will
execute 4 times. During execution, the gl_VertexID will be 0, 1, 2, 
or 3. With that, we generate a quad with the following formula

	    gl_Position = vec4(
	    mod( gl_VertexID, 2 ),
	    mod( gl_VertexID / 2, 2 ),
	    0.0,
	    1.0 );
		
Lets break that down
gl_Position is a vec4, with the following variables: X, Y, Z, W
X will be mod( gl_VertexID, 2 )
Y will be mod( gl_VertexID / 2, 2 )
Z will always be 0
W will always be 1

mod, of course, means modulus. mod(a, b) means a % b.

Lets focus on X,
0 % 2 = 0
1 % 2 = 1
2 % 2 = 0
3 % 2 = 1

Lets focus on Y,
when we divide gl_VertexID by 2, this is integer division,
so if we have a decimal as a result, it rounds down
0/2 = 0, 0 % 2 = 0
1/2 = 0, 0 % 2 = 0
2/2 = 1, 1 % 2 = 1
3/2 = 1, 1 % 2 = 1

Finally, the vertex buffer generates the following (x, y) vertices:
(0, 0), (1, 0), (0, 1), (1, 1)		which gives us a square!

In the fragment shader, we use a similar concept
gl_FragCoord gives us the X and Y positions of the pixel being processed,
so gl_FragCoord.y gives us the Y coordinate of the pixel being procesesd.
Applying mod to the Y coordinate gives us a fancy effect
We also use gl_PrimitiveID to get the polygon index being drawn.
There aer two triangles being drawn, so gl_PrimitiveID can be 0 or 1,
which writes to the green color channel of the pixel

There you go! Bufferless rendering

[Where this can be used]

This can be used in 2D textures to generate the vertices of the sprite,
then you can draw the texture on those vertices.

gl_PrimitiveID can be used to cap the number of polygons being drawn.
If you want to limit your scene to 1000 polygons, you can use

if(gl_PrimitiveID > 1000)
	discard;
