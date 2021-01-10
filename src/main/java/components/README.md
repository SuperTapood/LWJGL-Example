# The Components Package

- [The Font Renderer](#font-renderer)
- [The Sprite](#sprite)
- [The Sprite Renderer](#sprite-renderer)
- [The Sprite Sheet](#sprite-sheet)

## Font Renderer
An empty class for now.

```java
public class FontRenderer extends Component
```
The FontRenderer class extends the Jade.Component abstract class (see **#ADD REFERENCE**)

```java
@Override
public void start(){
    if (gameObject.getComponent(SpriteRenderer.class) != null){
        System.out.println("Found Font Renderer");
    }
}
```
This function overrides the start function of the abstract class Jade.Component (see **#ADD REFERENCE**).
It's current only use is for testing, which is why it just prints to the console that it works, and 
isn't being invoked anywhere in the code.


## Sprite

```java
private Texture texture;
private Vector2f[] texCoords;
```
texture and texCoords are variables that will be used by other classes to figure this sprite out,
so this class ends up being a "named tuple".

```java
public Sprite(Texture texture){
    this.texture = texture;
    Vector2f[] texCoords = {
            new Vector2f(1, 1),
            new Vector2f(1, 0),
            new Vector2f(0, 0),
            new Vector2f(0, 1)
    };
    this.texCoords = texCoords;
}
```
This initializer uses the default texture coordinates, and is what used mostly in the project (is it?)

```java
public Sprite(Texture texture, Vector2f[] texCoords){
    this.texture = texture;
    this.texCoords = texCoords;
}
```
The complete initializer.

```java
public Texture getTexture() {
    return texture;
}

public Vector2f[] getTexCoords() {
    return texCoords;
}
```
These are a couple getters so that we can still get those items, but make them read only.


## Sprite Renderer

```java
public class SpriteRenderer extends Component
```
This class, just like the Font Renderer, extends the Jade.Component class (see **#ADD REFERENCE**).

```java
private Vector4f color;
private Sprite sprite;
private Transform lastTransform;
private boolean isDirty = false;
```
Pre-declaring the variables that will be needed throughout this class. <br>
```Vector4f color``` - The color of the sprite (white when only provided with sprite) <br>
```Sprite sprite``` - The sprite itself (null when only provided with color) <br>
```Transfrom lastTransform``` - The last transformation that was made. <br>
```boolean isDirty``` - Used to dictate whether this element needs to be redrawn. <br>



```java
public SpriteRenderer(Vector4f color){
    this.color = color;
    this.sprite = new Sprite(null);
    this.isDirty = true;
}

public SpriteRenderer(Sprite sprite){
    this.sprite = sprite;
    this.color = new Vector4f(1, 1, 1, 1);
    this.isDirty = true;
}
```
Two different constructors to build the Sprite Renderer, using both a color and a sprite.

```java
@Override
public void start(){
    this.lastTransform = gameObject.transform.copy();
}
```
When we start the sprite renderer, we want to set the last transform to the current one.

```java
@Override
public void update(float dt){
   if (!this.lastTransform.equals(this.gameObject.transform)){
       this.gameObject.transform.copy(this.lastTransform);
       isDirty = true;
   }
}
```
This function is being called every frame. It's sole purpose is to check whether the sprite
changed and flag it if it did.

```java
public Vector4f getColor() {
    return color;
}

public Texture getTexture() {
    return sprite.getTexture();
}

public Vector2f[] getTexCoords() {
    return sprite.getTexCoords();
}
```
Various getters for private attributes.

```java
public void setSprite(Sprite sprite){
    this.sprite = sprite;
    this.isDirty = true;
}

public void setColor(Vector4f color){
    if (!this.color.equals(color)) {
        this.color.set(color);
        this.isDirty = true;
    }
}
```
These setters exist so that we can automatically flag the component whenever it is changed so that
it will be re-rendered.

```java
public boolean isDirty(){
    return this.isDirty;
}

public void setClean(){
    this.isDirty = false;
}
```
Two methods to manage the dirty flag for this class.

## Sprite Sheet
```java
private Texture texture;
private List<Sprite> sprites;
```
The only two variables we will need for this class: <br>
```Texture texture``` - The entire texture sheet as a texture. <br>
```List<Sprite> sprites``` - the list of sprites extracted from the sprite sheet. <br>

```java
public SpriteSheet(Texture texture, int spriteWidth, int spriteHeight, int numSprite, int spacing){
    this.sprites = new ArrayList<>();
    this.texture = texture;
    int currentX = 0;
    int currentY = texture.getHeight() - spriteHeight;
```
This is the constructor for the Sprite Sheet class. 
The constructor takes the following arguments:
<br>```Texture texture``` - The entire sprite sheet as a sprite object.
<br>```int spriteWidth``` - The width of a single sprite.
<br>```int spriteHeight``` - The height of a single sprite.
<br>```int numSprite``` - The total number of sprites contained within the sheet.
<br>```int spacing``` - The space between each sprite both for the x and y in pixels.

```java
for (int i = 0; i < numSprite; i++){
    float topY = (currentY + spriteHeight) / (float)texture.getHeight();
    float rightX = (currentX + spriteWidth) / (float)texture.getWidth();
    float leftX = currentX / (float)texture.getWidth();
    float bottomY = currentY / (float)texture.getHeight();
```
This is the sprite extraction for loop, it has been broken to pieces to make it more 
manageable
```java
    Vector2f[] texCoords = {
        new Vector2f(rightX, topY),
        new Vector2f(rightX, bottomY),
        new Vector2f(leftX, bottomY),
        new Vector2f(leftX, topY)
    };
```
```java
    Sprite sprite = new Sprite(this.texture, texCoords);
    this.sprites.add(sprite);
```
```java
    currentX += spriteWidth + spacing;
    if (currentX >= texture.getWidth()){
        currentX = 0;
        currentY -= spriteHeight + spacing;
    }
}
```
```java
public Sprite getSprite(int index){
    return this.sprites.get(index);
}
```