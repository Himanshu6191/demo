import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import pygame
import sys
from sklearn.linear_model import LinearRegression
from  sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error,r2_score
np.random.seed(42)
hours = pd.date_range(start='2023-01-01 00:00:00', periods=200, freq='H')
traffic_volume = 200 + 100 * np.sin(np.linspace(0, 4 * np.pi, 200)) + np.random.normal(0, 20, 200)
df = pd.DataFrame({'Timestamp': hours, 'TrafficVolume': traffic_volume})
df.set_index('Timestamp', inplace=True)
for i in range(1,4):
    df[f'Lag_{i}'] = df['TrafficVolume'].shift(i)
df.dropna(inplace=True)
X = df[['Lag_1', 'Lag_2', 'Lag_3']]
y = df['TrafficVolume']
X_train, X_test, y_train, y_test = train_test_split(X, y, shuffle=False, test_size=0.2)

model = LinearRegression()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

plt.figure(figsize=(10, 5))
plt.plot(y_test.index, y_test.values, label='Actual Traffic')
plt.plot(y_test.index, y_pred, linestyle='--', label='Predicted Traffic')
plt.title("Traffic Volume Prediction")
plt.xlabel("Time")
plt.ylabel("Vehicle Count")
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.savefig('traffic_plot.png')  # Save the plot as an image

pygame.init()

WINDOW_WIDTH = 800
WINDOW_HEIGHT = 600

screen = pygame.display.set_mode((WINDOW_WIDTH, WINDOW_HEIGHT))
pygame.display.set_caption("Traffic Volume Prediction GUI")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# Fonts
font = pygame.font.SysFont(None, 32)
big_font = pygame.font.SysFont(None, 48)

# Load image
plot_image = pygame.image.load('traffic_plot.png')
plot_image = pygame.transform.scale(plot_image, (750, 400))  # Scale to fit window
