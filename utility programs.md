## SVG to Canvas

```
function convertInputToCanvasCommands(inputString) {
    const commands = inputString.split(' ');

    let canvasCommands = '';
    let currentX = 0;
    let currentY = 0;
    let pathCount = 0;

    for (let i = 0; i < commands.length; i++) {
        switch (commands[i]) {
            case 'M':
                if (pathCount > 0) {
                    canvasCommands += 'ctx.closePath();\n';
                    canvasCommands += 'ctx.stroke();\n\n';
                }
                pathCount++;
                canvasCommands += `// Path ${pathCount}\n`;
                canvasCommands += 'ctx.beginPath();\n';
                currentX = parseFloat(commands[i + 1]);
                currentY = parseFloat(commands[i + 2]);
                canvasCommands += `ctx.moveTo(${currentX}, ${currentY});\n`;
                i += 2;
                break;
            case 'C':
                const [cp1x, cp1y, cp2x, cp2y, x, y] = [
                    parseFloat(commands[i + 1]), parseFloat(commands[i + 2]),
                    parseFloat(commands[i + 3]), parseFloat(commands[i + 4]),
                    parseFloat(commands[i + 5]), parseFloat(commands[i + 6])
                ];
                canvasCommands += `ctx.bezierCurveTo(${cp1x}, ${cp1y}, ${cp2x}, ${cp2y}, ${x}, ${y});\n`;
                currentX = x;
                currentY = y;
                i += 6;
                break;
            case 'V':
                const yV = parseFloat(commands[i + 1]);
                canvasCommands += `ctx.lineTo(${currentX}, ${yV});\n`;
                currentY = yV;
                i += 1;
                break;
            case 'H':
                const xH = parseFloat(commands[i + 1]);
                canvasCommands += `ctx.lineTo(${xH}, ${currentY});\n`;
                currentX = xH;
                i += 1;
                break;
            case 'Z':
                canvasCommands += 'ctx.closePath();\n';
                canvasCommands += 'ctx.stroke();\n\n';
                break;
            case 'S':
                const [cp2xS, cp2yS, xS, yS] = [
                    parseFloat(commands[i + 1]), parseFloat(commands[i + 2]),
                    parseFloat(commands[i + 3]), parseFloat(commands[i + 4])
                ];
                const cp1xS = currentX * 2 - parseFloat(commands[i - 2]);
                const cp1yS = currentY * 2 - parseFloat(commands[i - 1]);
                canvasCommands += `ctx.bezierCurveTo(${cp1xS}, ${cp1yS}, ${cp2xS}, ${cp2yS}, ${xS}, ${yS});\n`;
                currentX = xS;
                currentY = yS;
                i += 4;
                break;
            // Add cases for other commands as needed
            default:
                // Handle unrecognized commands or skip if not needed
                break;
        }
    }

    return canvasCommands;
}
```

```
// Example usage
const inputString = 'M 10.9999 0 C 11.0087 0 11.0175 0 11.0263 0 C 26.1718 0.0522 25.1342 22.552 11.0263 28.8481 V 28.8716 C 11.0175 28.8677 11.0087 28.8638 10.9999 28.8599 C 10.9911 28.8638 10.9823 28.8677 10.9735 28.8716 V 28.8481 C -3.1344 22.552 -4.172 0.0523 10.9735 0.0001 C 10.9823 0.0001 10.9911 0 10.9999 0 M 2.1101 12.23 C 1.8814 12.4691 1.4636 16.4019 3.6176 18.4619 C 5.7716 20.5218 9.7852 19.9227 9.7852 19.9227 C 9.7852 19.9227 10.5571 15.5177 8.4031 13.4578 C 6.2491 11.3979 2.1101 12.23 2.1101 12.23';
const canvasCommands = convertInputToCanvasCommands(inputString);
console.log(canvasCommands);

```