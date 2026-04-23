+++
title = "Ray Tracer from Scratch"
date = 2026-04-23
description = "A software renderer built from first principles following Gabriel Gambetta's Computer Graphics from Scratch. Chapters 1–2: canvas abstraction, sphere intersection, basic shading."

[extra]
type = "graphics / C"
github = "https://github.com/impeccableshoddy/fromScratch"
toc = true
+++

## The Inception

So for the last couple of weeks I have been fiddling with this thing called a raytracer and till now I don't really think I'm exactly sure about what it is but let me begin from what I know and try to figure out the rest as I work to explain this for you.

**TL;DR**

- I rant about raytracers.
- I realize mid rant how we are just calculating for the intersection between the ray equation and the object equation and put a pre-defined color in that specific pixel coordinate.
- Birth of a self-fulfilling prophecy.

---

We begin with something that we call the **Canvas** which is our actual screen of output and is measured in pixels. It is where we will be displaying our final product.

Now as we will inevitably be dealing with arithmetic operations in our line of work, first of all we will very conveniently change our canvas's original axis system which mostly (*I don't know if it's in all cases*) starts from the top left corner of the screen. So `(0,0)` and `x` increases towards the right side and `y` increases downwards.

And we convert this is into a *Cartesian Axis System*, which means the origin should be at the center of the screen, which is how we actually visualize and calculate our math.

We also do this, because pixels on default go from 0 to upwards, but by changing the system, we can very easily use our coordinate system and use negative no.s to represent something like `(-1,1)` for a pixel in the 2nd quadrant in the upper left corner.

Now we don't actually change the axis points of the canvas (*not sure if we even can change it to begin with*) we simply make our computer calculate it for every pixel by running it through a formula, such that the output will be calculated fitting our *cartesian* system, which we then go onto use for our mathematical formulas.

For example we use `datatype var_name_px = px - (canvas_width/2)` for the `x` axis which makes it so we account for the raw pixel and count it as if it was from the center, simulating our cartesian plane.

Same for the `y` axis, but the equation is a bit different, for example: `(canvas_height/2) - py` usually we define these var names as `Cx` & `Cy` respectively to relate them easily to the new canvas coordinates.

---

## Deep in Ordeal

Whoa whoa whoa, a lot went down there, let's slow down, so we have got our new canvas axis now and we know what this canvas is to begin with, but in-case you forgot, originally our whole purpose has been to figure out about this thing called ***raytracer***. Which is based upon 3 base elements and one of those is our beloved **canvas**.

The other two is what we call as the **camera** - a 3d point in space (*imaginary*) which we usually consider to be at the the origin O (0,0,0) and **viewport** which can be considered as a screen of defined dimensions (in **real world units**, not pixels) in front or top of (somewhere) the canvas and after or behind the camera, such that it lies somewhere in-between the canvas and camera.

This can be better visualized as if a person is standing inside a room and looking out of the window at the outside world, then if we consider their eye to be a single point in space and decide that coordinate to be `O (0,0,0)`, then that is our *camera*.

The window or screen at a finite distance in-front of this camera (measured in real world units) is our *viewport* and anything beyond the viewport which can be seen out in the real world are the elements which are meant to be visible.

We don't know their exact distance, we just know that they are out there and our job is to map that relationship from the window (*viewport*) to each individual pixel on the canvas.

We map the **Pixel**`(x,y)` to **Viewport**`(Vx,Vy)` which tell us where to look in the window to find the color of the outside world from the cam's perspective. This is why we need the `Cx` and `Cy` to begin with!.

We are essentially imagining whatever image we want already exists upon the canvas (the real world), and we don't care about their distance.

And even among them we only care about those which are visible. In real world light travels and hits an objects and is reflected back onto our eyes, that's how we "see" things. But because it would be impossible computation wise to simulate light and it's reflective properties in such a large scale, we say that whatever doesn't reach our eyes we don't care about them. Thus effectively only tracing back visible light in reverse from our eyes (*in this case the camera*) and tracing it's path as it passes through our window (*the viewport*) and then unto the canvas (*as it gets scaled appropriately to be mapped onto the pixels on canvas*).

Now let's take a moment and consider what really happened here, because for the longest time I tried thinking about this exactly as I explained, as if we were trying to trace back the ray to something that is already drawn out there and get back it's color.

And it doesn't make sense. How can something be already drawn out there when our goal is to draw something to begin with? And that's because it's not.

---

## Phew, We Survived!

The whole tracing business is to just get the one single information, to know if it hit any object. And these objects are actually predefined with what color they are supposed to be.

Thus we just determine if the ray equation intersects this shape equation and if the answer is yes we return some predefined color that's supposed to be returned if this certain kind of object is supposed to be hit.

And then we simply put that color onto that pixel on canvas. *Preposterous!*

Ever heard of the self fulfilling prophecy? weirdly enough, this reminds me of it. Because the object never existed, we define some mathematical structure for it at where it's supposed to be if it was indeed there, and they run our rays, get back the coordinates of intersection and put the colors ourselves thus fulfilling our prophecy.

Something weird about that.

Now the result of all that is this right here -

{{ figure(src="output.svg", alt="A basic ray tracer", caption="Fig 1: A basic ray tracer implementation", id="fig1") }}

Not that glorious eh? (*how dare you!*) but don't be too disheartened, this is just the most basic implementation which I have learned so far now, I will be sure to be back with more interesting stuff.

---

## You are a fool to have believed it's ending so soon!

But before that let's get a bit more into this and really understand a bit of the *how*'s of what I have accomplished and how you can too. Here's what you need to have set up before we get into code -

 ### Environment and Tools

- **Compiler**: GCC 15.2.1
- **C standard**: C23 (`-std=c23` or `-std=gnu23`)
- **External Libraries**: `stdio.h`, `math.h`
- **Build method**: Direct compilation of multiple `.c` files via the command line.
- **Test command**: `gcc -std=c23 -Wall -Wpedantic *.c -o raytracer`

### File structure

``````
.
├── output.ppm
├── ppm.c
├── ppm.h
├── raytracer.c
└── raytracer.h
``````

### How to Run

1. Open your terminal and **navigate to the project directory** (where `raytracer.c` and `ppm.c` are located).

2. Compile: 

   ```bash
   gcc -std=c23 -Wall -Wpedantic *.c -o raytracer
   ```

3. Run: 

   ```bash
   ./raytracer
   ```

4. View the output:
   The program generates `output.ppm` in the current directory.

   - **Linux/macOS**: Use `feh` to view it: 

     ```bash
     feh output.ppm
     ```

     if `feh` is not installed:

     - Ubuntu/Debian: `sudo apt install feh`
     - macOS(with Homebrew): `brew install feh`

   - **Windows**: 
     PPM files aren’t natively supported. Use one of these options:

     - **IrfanView** ([download here](https://www.irfanview.com/)) or **GIMP** ([download here](https://www.gimp.org/)) - open `ouput.ppm` directly.

     - **ImageMagick**: Convert to PNG: 

       ```bash
       magick output.ppm output.png
       ```

       Then open `output.png` with any viewer.

     - **Online tool**: Use [AnyConv](https://anyconv.com/ppm-to-png-converter/) to convert PPM to PNG in your browser.

## That was just the prelude

So now let's actually understand the code. (*yes finally*)

As we have already established, this whole thing works based on finding if the ray equation intersects with the object equation, and thus one might feel audacious enough to pay more attention to them.

And we know what we are here lads, let's get right into it.

Now before we even get to talk about the ray equation, we will have to take a look back at where we have stopped, calculating the `Cx` and the `Cy`, which has been for *quite a while* now.

Which if you don't remember (*I don't blame you*) was us transforming the default pixel coordinates of the canvas to suit our *Cartesian* axis system for easier representation of vector points and common math.

Our next step now is to establish the direction vector D which is nothing but the direction of the ray we are tracing back from our eye.

Think about it: we have our Camera at `O(0, 0, 0)` and we have a point on our Viewport window, Which let us consider as `V(Vx, Vy, Vz)`. If we draw a line starting from our eye to this point on the viewport, that line has a direction. That is our `D`.

Mathematically, then:

​	`D = V - O`

Since we are smart (or lazy) enough to put our camera at the origin `(0, 0, 0)`, the subtraction is just a bit easier than Sisyphus's eternal trek up a mountain in the underworld.

Thus `D` just ending up as the coordinates of the point `V` on our Viewport. Where `Vx = Cx * (viewport_width / canvas_width)`, `Vy = Cy * (viewport_height / canvas_height)` and `Vz` being the distance `d` in the *projection plane*. Which is the distance between the camera and the Viewport.

Let's catch up a bit first before we move further, so far we know what the canvas is and what we are doing and how and why we are doing it to convert the raw pixels to our Cartesian system and then we even get the Direction from there.

But we have missed one essential part which is defining the Canvas itself! But don't fret, it's not that scary, quite simple in-fact such that our Canvas is just a 2d array of definite dimensions - `canvas[canvas_height][canvas_width]`. (And yes be sure to provide the canvas_height first as that is the number of our rows and determines how long our canvas will be!)

We haven't mentioned it explicitly anywhere yet, but after defining the canvas we also have to make sure and not forget to define our origin O.

Now we are getting somewhere! and we have to define a bit more stuff before we can effectively get to the meat and potatoes, starting with the `viewport_width`, `viewport_height` and projection plane distance `d` (Which will be our `Vz`). We finish by defining our mathematical structures (*the godforsaken things*), in our case which is a sphere, but defining its center, radius and color in an array of sorts. Let's call it `S`.

And finally get to the loop in main from which we get our raw pixels and input them in our functions. Here's my implementation of it - 

```c, linenos
for (int py = 0; py < CANVAS_HEIGHT; py++) {
    for (int px = 0; px < CANVAS_WIDTH; px++) {
        const float Cx = px - CANVAS_WIDTH / 2.0f;
        const float Cy = CANVAS_HEIGHT / 2.0f - py;

        D = canvas_to_viewport(Cx, Cy, VIEWPORT_WIDTH, VIEWPORT_HEIGHT, d);

        Color color = trace_ray(O, D, S, 3, 1.0f, INFINITY); // where Color is a struct of formation
           // { int r, g, b; }

        put_pixel(canvas, px, py, color);
        // And put_pixel being a function which
        // just puts given color in the specified
        // coordinates on the canvas.
    }
}
```

Now you might have noticed that we missed the most important function there, which is the trace_ray(). Because it deserves it's own section.

Let's first start with the inputs `O` and `D` are the origin as discussed respectively and `S` being our newly established sphere array / array of spheres.

Now let's take a look at the other 3 arguments and how they look from inside the function - 

```c, linenos
Color trace_ray (Vec3 O, Vec3 D, Sphere *S, int num_of_spheres, float t_min, float t_max) 
{    
    Color BG_COLOR = { 254, 254, 254 };
    
    float closest_t = t_max;
    Sphere *closest_sphere = NULL;
    float t1, t2;

    for (int i = 0; i < num_of_spheres; i++) 
    {
        intersect_ray_sphere(O, D, &S[i], &t1, &t2);

        if (t_min < t1 && t1 < t_max && t1 < closest_t) {
            closest_t = t1;
            closest_sphere = &S[i];
        }
        if (t_min < t2 && t2 < t_max && t2 < closest_t) {
            closest_t = t2;
            closest_sphere = &S[i];
        }
    }

    if (closest_sphere == NULL) return BG_COLOR;

    return closest_sphere -> color;    
}
```

*Ew, what is that?!* that was my reaction when I read my own code trying to understand it just 2 days later! But no need to fear, since then I have developed a great tolerance for such atrocities and shall take a look at it for you instead and try to explain it.

So as established, we take in the origin, direction and the array of spheres and the other 3 arguments being the no. of spheres, minimum t and maximum t (*more on this in just a bit*).

This `t` is actually a point along the ray at any given time. Representing the distance along the ray from it's origin. And how does that matter you ask? well it matters very much my friend.

Remember that ray equation we have been fussing so much about? well turns out, it's a pretty big part of it.

​	`P = O + t*D`

where `P` is a point in space, `O` is the origin, `D` is our  normalized direction vector, and `t` is a scalar that determines how far along the ray the point lies.

Positive values of `t` place `P` in front of the origin (along the ray), while negative values place it behind.

In ray tracing, we only consider intersections where `t > 0` aka `t` is positive. And typically within a valid range `[t_min, t_max]`. `t_max` usually being `INFINITY` or some other absurdly large number.

Now that we have established that, you can scroll back to our code snippet above and very easily make out that it is thus used to act out a bunch of conditional statements. To perform checks for each value of `t` returned by (updated by) the function `intersect_ray_sphere(O, D, &S[i], &t1, &t2);`.

Which takes in the origin `O`, direction `D` and our `t1` and `t2` and does something like this :

```c, linenos
void intersect_ray_sphere (Vec3 O, Vec3 D, Sphere *S, float *t1, float *t2)
{
    Vec3 C = S -> center;
    float r = S -> rad;
    
    float a = vec3_dot(D, D);
    float b = 2 * vec3_dot(vec3_sub(O, C), D);
    float c = vec3_dot(vec3_sub(O, C), vec3_sub(O, C)) - r*r;

    float discriminant = b*b - 4 * a * c;

    if (discriminant >= 0) 
    {
        *t1 = (-b + sqrt(discriminant)) / (2 * a);
        *t2 = (-b - sqrt(discriminant)) / (2 * a);
    }
    else *t1 = INFINITY, *t2 = INFINITY;
}
```

Starting with vector point `C` which stores the given sphere's center coordinate, and `r` storing the sphere's radius. We then go onto perform a bunch of stuff, which won't make sense until we look at the sphere equation, which we haven't done until now -

​	`(P - C) . (P - C) = r^2`

Where the `.` represents the Dot product among vectors. Which we do in our code by - 

```c
float vec3_dot (Vec3 A, Vec3 B) {
    return (float)A.x * B.x + A.y * B.y + A.z * B.z;
}
```

And now we will derive our way into that abomination looming above us by putting the previous ray equation in this sphere equation - 

1. `(O + t*D - C) . (O + t*D - C) = r^2`

2. Let `OC = O - C`, where we do vector subtraction by - 

   ```c
   Vec3 vec3_sub (Vec3 A, Vec3 B) {
       return (Vec3){ A.x - B.x, A.y - B.y, A.z - B.z };
   }
   ```

   and simplify our equation further to - 
   	`(OC - t*D) . (OC - t*D) = r^2`

3. Expanding the dot product - 
   `OC.OC + 2t(D.OC) + t^2(D.D) = r^2`

4. Rearranged into quadratic we get - 
   `t^2(D.D) + 2t(D.OC) + (OC.OC - r^2) = 0`

*huff!* and now we just simplify that for easier use and understanding and assign as `a = D.D` and so on, to get the classic form `at^2 + bt + c = 0`.

Now take a look at the above abomination once again and see if it's a bit easier on the eyes. (*Hopefully! If not, keep looking, as it gets easier with time. For severe consideration one might consider taking a look at others abomination to truly appreciate ones own >_<*)

Okay so we are almost done now since we have made sense of what the `a`, `b` and `c` are we can now make sense of the rest pretty easily.

So we define the discriminant next which is just the `b^2 - 4ac` stuff which we are used to seeing inside the square roots.

It tells us how many real solutions exist for the ray-sphere intersection.

- **If discriminant < 0**: No real solutions aka our **ray misses the sphere**.
- **If discriminant = 0**: One real solutions aka our **ray just grazes the sphere**(*tangent*).
- **If discriminant > 0**: Two real solutions aka our **ray passes through the sphere**(*enters and exits*).

Thus we only check for when the discriminant is greater than equal to 0 because that is when our ray meaningfully intersects the sphere or real intersections exist.

The last part is the actual conditional which is just saying that if the discriminant passes our criteria, calculate the 2 roots and store their value in `t1` and `t2`. Otherwise make `t1` and `t2` store `INFINITY`.

And now we get back to our `trace_ray()` function once again and take a look at it with our enlightened eyes, and see what it does.

So we have already established `t1`, `t2` and how `intersect_ray_sphere` works in this. The other thing being `BG_COLOR` indicating the background color (*crazy*) being defined.

Now we are left with the `closest_sphere` and `closest_t` which store `NULL` and `INFINITY` respectively at the start.

Then we loop through our number of spheres and for each sphere we calculate it's roots and see if the ray intersects or not. And if it intersects (in which case we get real values of `t1` and `t2`) we change the `closest_t` to the current `t` and the `closest_sphere` to the current sphere.

And at last we check if `closest_sphere` is still `NULL` if yes, we just return the background color indicating that the ray didn't hit any of our defined objects, otherwise we return the color of the current object it has hit.

## The End (Seriously .-.)

Well uhh, that was it kind of. (*evades the praises hurled my way*) NO REALLY!

Ummm okay you got me, just a bit more. (*ouch, I'm sorry, I swear we are truly done this time!*) So we have our returned color as a result of our beautiful `trace_ray()` which if you still remember the main (*now would be a good time to give it a revisit otherwise*) then uses to put color onto that pixel.

But we have a problem, if you have noticed, we haven't mentioned anything about printing or output this whole time. That's because that's what we will be doing now. And it's done by another of our beautiful functions `write_ppm()` right here - 

```c,linenos
void write_ppm ( const char *filename, Color pixels[][CANVAS_WIDTH])
{
    FILE *file = fopen(filename, "w");
    if (!file) { printf("Error opening file\n"); return; }

    fprintf(file, "P3\n%d %d\n%d\n", CANVAS_WIDTH, CANVAS_HEIGHT, MAX_VAL);

    for (int i = 0; i < CANVAS_HEIGHT; i++)
        for (int j = 0; j < CANVAS_WIDTH; j++)
            fprintf(file, "%d %d %d\n", pixels[i][j].r,
                                        pixels[i][j].g,
                                        pixels[i][j].b);

    fclose(file);
}
```

Which takes file name and the canvas array as arguments and then creates a writable file with that filename and outputs a ppm format onto it, by running a loop and printing one pixel at a time until the whole canvas is printed.

That's it, that's genuinely it. All that to get our *[Fig 1](#fig1)* which you saw above.

---

### Source Code

For those who want to dig into the header files or see how the PPM logic is actually implemented without the "rant" filtering, you can find the full source code here: **[fromScratch on GitHub](https://github.com/impeccableshoddy/fromScratch/tree/main/Graphics-from-scratch)**

---

Hope had fun and learned something.

And if you followed along, *kudos!*

That's it for today and see ya until next time! ;)
