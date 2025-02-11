const sharp = require('sharp');
const fs = require('fs');


//x1: right -> left
//x2: left -> right
//y1: down -> top
//y2: top -> down 
const svg = "<svg width=\"400\" height=\"400\" version=\"1.1\" xmlns=\"http://www.w3.org/2000/svg\">" +
    "  <defs>" +
    "      <linearGradient id=\"Gradient2\" x1=\"1\" x2=\"0\" y1=\"0\" y2=\"0\">" +
    "        <stop offset=\"0%\" stop-color=\"white\" stop-opacity=\"50\"/>" +
    "        <stop offset=\"50%\" stop-color=\"white\" stop-opacity=\"0\"/>" +
    "      </linearGradient>" +
    "  </defs>" +
    "<rect x=\"0\" y=\"0\" width=\"400\" height=\"400\" fill=\"url(#Gradient2)\"/>" +
    "</svg>";

sharp(Buffer.from(svg))
    .toBuffer()
    .then(
        (linearGradient) => {
            sharp('input_image.jpg') 
                .resize(400, 400)
                .extract({
                    height: 400,
                    left: 0,
                    top: 0,
                    width: 400
                })
                .overlayWith(linearGradient, {

                })
                .png()
                .toBuffer()
                .then(function (outputBuffer) {
                    fs.writeFile('out.jpg', outputBuffer, function (err) { });
                }).catch((e) => console.log(e));
        }).catch((e) => console.log(e));


![](https://komarev.com/ghpvc/?username=ConsCXius&color=d60d02&style=flat-square&label=_!_)
