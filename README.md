
var tex = PIXI.RenderTexture.create(W1, H1);
var spr = new PIXI.Sprite(PIXI.Texture.WHITE);
spr.anchor.set(0.5);
spr.width = W2;
spr.height = H2;
spr.position.set(W1/2, H1/2);
spr.filters = [blurFilter];
renderer.render(spr, tex);

//use tex as a texture for mask sprite or masked sprite
