// Stars & Space


#include <UTFTGLUE.h>
#include <math.h>

UTFTGLUE myGLCD(0, A2, A1, A3, A4, A0);

struct Star {
  float x, y, z;
  float size;
  int color;
};

const int numStars = 301; // Increase by 1 to include the planets
Star stars[numStars];
Star originalPlanetState; // Store the original configuration of the first planet

Star secondPlanet; // Declare the second planet

const unsigned int VGA_ORANGE = 0xFC40; // Orange color
const unsigned int VGA_BROWN = 0x8200; // Brown color

float rotationAngle = 0; // Rotation angle of the first planet
float rotationSpeed = 0.05; // Rotation speed of the first planet

void setup() {
  randomSeed(analogRead(0));

  myGLCD.InitLCD();
  myGLCD.clrScr();

  for (int i = 0; i < numStars - 1; i++) {
    stars[i].x = random(-1000.0, 1000.0);
    stars[i].y = random(-1000.0, 1000.0);
    stars[i].z = random(100.0, 3000.0);
    stars[i].size = random(0.5, 2.0);
    stars[i].color = VGA_WHITE;
  }

  // Configure the first planet
  stars[numStars - 2].x = 0; // Initial x position of the first planet (at the center)
  stars[numStars - 2].y = 0; // Initial y position of the first planet (at the center)
  stars[numStars - 2].z = 2000; // Initial distance of the first planet (greater than stars)
  stars[numStars - 2].size = 1.0; // Initial size of the first planet
  stars[numStars - 2].color = VGA_ORANGE; // Orange color of the first planet

  // Store the original configuration of the first planet
  originalPlanetState = stars[numStars - 2];

  // Configure the second planet
  secondPlanet.x = 0; // Initial x position of the second planet
  secondPlanet.y = 0; // Initial y position of the second planet
  secondPlanet.z = 3000; // Initial distance of the second planet
  secondPlanet.size = 1.0; // Initial size of the second planet
  secondPlanet.color = VGA_BROWN; // Brown color of the second planet
}

void loop() {
  unsigned long startTime = micros();

  drawScene();

  unsigned long elapsedTime = micros() - startTime;
  // Adjust the waiting time to maintain the speed
  if (elapsedTime < 0) {
    delayMicroseconds(abs(elapsedTime) * 5); // Increase the waiting time by 5 times
  }
}

void drawScene() {
  for (int i = 0; i < numStars - 2; i++) {
    int oldScreenX = (int)(240 + (stars[i].x / stars[i].z) * 240);
    int oldScreenY = (int)(160 + (stars[i].y / stars[i].z) * 160);
    if (oldScreenX >= 0 && oldScreenX < 480 && oldScreenY >= 0 && oldScreenY < 320) {
      myGLCD.setColor(0, 0, 0);
      myGLCD.fillCircle(oldScreenX, oldScreenY, (int)stars[i].size);
    }

    stars[i].z -= 75.0; // Adjust the speed of movement
    if (stars[i].z <= 0) {
      stars[i].x = random(-1000.0, 1000.0);
      stars[i].y = random(-1000.0, 1000.0);
      stars[i].z = random(500.0, 3000.0);
    }

    int screenX = (int)(240 + (stars[i].x / stars[i].z) * 240);
    int screenY = (int)(160 + (stars[i].y / stars[i].z) * 160);
    if (screenX >= 0 && screenX < 480 && screenY >= 0 && screenY < 320) {
      myGLCD.setColor(stars[i].color);
      myGLCD.fillCircle(screenX, screenY, (int)stars[i].size);
    }
  }

  // Draw and move the first planet
  int oldPlanetX = (int)(240 + (stars[numStars - 2].x / stars[numStars - 2].z) * 240);
  int oldPlanetY = (int)(160 + (stars[numStars - 2].y / stars[numStars - 2].z) * 160);
  if (oldPlanetX >= 0 && oldPlanetX < 480 && oldPlanetY >= 0 && oldPlanetY < 320) {
    myGLCD.setColor(0, 0, 0);
    myGLCD.fillCircle(oldPlanetX, oldPlanetY, (int)stars[numStars - 2].size);
  }

  // Update the position and size of the first planet
  stars[numStars - 2].x += 10; // Adjust the x position of the first planet to move it forward
  stars[numStars - 2].size += 0.05; // Increase the size of the first planet

  if (stars[numStars - 2].z <= 0) {
    // Reset the position and size of the first planet when it reaches the screen boundary
    stars[numStars - 2] = originalPlanetState;
  }

    int planetX = (int)(240 + (stars[numStars - 2].x / stars[numStars - 2].z) * 240);
  int planetY = (int)(160 + (stars[numStars - 2].y / stars[numStars - 2].z) * 160);
  if (planetX >= 0 && planetX < 480 && planetY >= 0 && planetY < 320) {
    myGLCD.setColor(VGA_ORANGE); // Change the color of the first planet to orange
    myGLCD.fillCircle(planetX, planetY, (int)stars[numStars - 2].size);
  }

  // Draw and move the second planet
  int oldSecondPlanetX = (int)(240 + (secondPlanet.x / secondPlanet.z) * 240);
  int oldSecondPlanetY = (int)(160 + (secondPlanet.y / secondPlanet.z) * 160);
  if (oldSecondPlanetX >= 0 && oldSecondPlanetX < 480 && oldSecondPlanetY >= 0 && oldSecondPlanetY < 320) {
    myGLCD.setColor(0, 0, 0);
    myGLCD.fillCircle(oldSecondPlanetX, oldSecondPlanetY, (int)secondPlanet.size);
  }

  // Update the position and size of the second planet
  secondPlanet.x -= 7; // Adjust the x position of the second planet to move it backward
  secondPlanet.size += 0.03; // Increase the size of the second planet

  if (secondPlanet.z <= 0) {
    // Reset the position and size of the second planet when it reaches the screen boundary
    secondPlanet.x = 0;
    secondPlanet.y = 0;
    secondPlanet.z = 3000;
    secondPlanet.size = 1.0;
  }

  int secondPlanetX = (int)(240 + (secondPlanet.x / secondPlanet.z) * 240);
  int secondPlanetY = (int)(160 + (secondPlanet.y / secondPlanet.z) * 160);
  if (secondPlanetX >= 0 && secondPlanetX < 480 && secondPlanetY >= 0 && secondPlanetY < 320) {
    myGLCD.setColor(VGA_BROWN); // Change the color of the second planet to brown
    myGLCD.fillCircle(secondPlanetX, secondPlanetY, (int)secondPlanet.size);
  }
}

