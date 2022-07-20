---
layout: page
title: Pixel Sorting
description: Making glitch art via paralell processing.
img: assets/img/pixel_sorter/in_process.gif
importance: 3
category: all
---

As I've been experimenting with computer graphics, I had an interesting thought: Given that GLSL shaders are run in parallel, how could we "sort" pixels?

In a parallel environment, a pixel's neighbor is non-deterministic (could change if a parallel process swaps said neighboring pixel first). This could lead to infinite loops and other undefined behavior if not properly taken care of.

So what do we do? Well, there actually is a computing sub-field dedicated to paralell algorithms (including sorting). The particular algorithm I would like to highlight is the odd-even sort.

Essentially, on every iteration the odd-even sort will compare either odd or even adjacent pairs (hence the name) and swap if necessary. Once all pairs have been computed, the following iteration will switch indexing (i.e odd to even, or even to odd) and repeat the same process. A more in-depth explanation can be found on [here](https://en.wikipedia.org/wiki/Odd%E2%80%93even_sort).

By using this concept, I created a [fragment shader](https://www.khronos.org/opengl/wiki/Fragment_Shader) in [TouchDesigner](https://derivative.ca/) to "sort pixels" from left to right based on brightness. The code can be found below:

{% highlight glsl linenos %}

out vec4 fragColor;

uniform int Frame;
uniform float Threshold; // Pixel values below this threshold value will not move (adjusted based on personal preference).

// Calculate position of neighboring pixels. (taken from https://docs.derivative.ca/Write_a_GLSL_TOP).
vec2 input2DOffset(int texIndex, int xOffset, int yOffset)
{
	return vec2(vUV.s + (float(xOffset) * uTD2DInfos[texIndex].res.s),
				vUV.t + (float(yOffset) * uTD2DInfos[texIndex].res.t));
}

// Take the average of a pixel's RGB values.
float average(vec4 color) {
	return (color.r + color.g + color. b) / 3;
}

void main()
{
	// NOTE: vUV.st references UV Coordinate (Between 0-1).
	// NOTE: uTD2DInfos[0] refers to the first image loaded into the GLSL top (within TouchDesigner).

	// Whether to look at the left or right neighboring pixel. 
	int a = int(mod(floor(vUV.s * uTD2DInfos[0].res.z), 2) * 2 - 1) * int(mod(Frame, 2) * 2 - 1);
	
	// Check for OOB (i.e edge pixel).
	if(input2DOffset(0, a, 0).x < 0.0 || input2DOffset(0, a, 0).x > 1.) {
		fragColor = TDOutputSwizzle(texture(sTD2DInputs[0], vUV.st));
		return;
	}

	vec4 neighbor_pixel = texture(sTD2DInputs[0], input2DOffset(0, a, 0));
	vec4 curr_pixel = texture(sTD2DInputs[0], vUV.st);

	float neighbor_brightness = average(neighbor_pixel);
	float curr_brightness = average(curr_pixel);

	vec4 color = curr_pixel;

	// Check if the neighbor pixel was to the left or right of the current pixel and swap if necessary. 
	if(a < 0.) {
		if(curr_brightness > Threshold && curr_brightness <= neighbor_brightness) {
			color = neighbor_pixel;
		}
	} else {
		if(neighbor_brightness > Threshold && curr_brightness > neighbor_brightness) {
			color = neighbor_pixel;
		}
	}

	// TDOutputSwizzle ensures consistent behavior between Mac and PC versions.
	fragColor = TDOutputSwizzle(color);
}

{% endhighlight %}

The result is a pixel sorter that efficiently runs in parallel.

<div class="row justify-content-center">
    <div class="col-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/pixel_sorter/in_process.gif" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A visual of the algorithm in-process.
</div>

Stopping mid-way through can also create some pretty interesting "glitchy" effects. See below for some examples:

<div class="row justify-content-center">
    <div class="col-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/pixel_sorter/example_1.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A photo of Chinatown in NYC "glitched".
</div>

<div class="row justify-content-center">
    <div class="col-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/pixel_sorter/example_2.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A photo of me. Hi!
</div>