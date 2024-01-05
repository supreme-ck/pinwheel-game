const canvas = document.getElementById('wheelCanvas');
const ctx = canvas.getContext('2d');
const spinButton = document.getElementById('spinButton');
const resultDiv = document.getElementById('result');

const segments = ["$5", "$10", "$20", "SPIN AGAIN", "$10", "$100", "SPIN AGAIN", "$5"];
let totalPrize = 0;
let currentAngle = 0;
let spinning = false;

function drawWheel() {
    const segmentAngle = 2 * Math.PI / segments.length;
    ctx.clearRect(0, 0, canvas.width, canvas.height); // Clear canvas before redrawing

    for (let i = 0; i < segments.length; i++) {
        ctx.beginPath();
        ctx.moveTo(canvas.width / 2, canvas.height / 2);
        ctx.arc(canvas.width / 2, canvas.height / 2, 200, currentAngle + segmentAngle * i, currentAngle + segmentAngle * (i + 1));
        ctx.closePath();
        ctx.fillStyle = getRandomColor();
        ctx.fill();
        ctx.stroke();

        // Add text
        ctx.save();
        ctx.translate(canvas.width / 2, canvas.height / 2);
        ctx.rotate(currentAngle + segmentAngle * (i + 0.5));
        ctx.textAlign = "right";
        ctx.fillStyle = "black";
        ctx.fillText(segments[i], 190, 10);
        ctx.restore();
    }
}

function spinWheel() {
    if (spinning) return;
    spinning = true;
    let startAngle = currentAngle;
    let spinTime = 0;
    let spinTimeTotal = Math.random() * 3000 + 5000; // Random spin duration

    function rotateWheel() {
        spinTime += 30;
        if(spinTime >= spinTimeTotal) {
            stopRotateWheel();
            return;
        }

        // Spin with ease out effect
        let spinEaseOut = Math.sin(spinTime / spinTimeTotal * Math.PI / 2);
        currentAngle = startAngle + spinEaseOut * (2 * Math.PI) * 10; // Adjust for multiple rotations
        drawWheel();
        requestAnimationFrame(rotateWheel);
    }

    function stopRotateWheel() {
        spinning = false;
        let degrees = (currentAngle % (2 * Math.PI)) * (180 / Math.PI); // Convert to degrees
        let segmentSize = 360 / segments.length;
        let selectedSegmentIndex = Math.floor((360 - degrees) / segmentSize) % segments.length;
        let selectedSegment = segments[selectedSegmentIndex];
        
        if (selectedSegment !== "SPIN AGAIN") {
            totalPrize += parseInt(selectedSegment.substring(1)) || 0;
            resultDiv.innerHTML = "Total Prize: $" + totalPrize;
        }
    }

    rotateWheel();
}

function getRandomColor() {
    const letters = '0123456789ABCDEF';
    let color = '#';
    for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
    }
    return color;
}

spinButton.addEventListener('click', spinWheel);
drawWheel();
