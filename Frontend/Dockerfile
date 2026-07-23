FROM python:3.11-slim

WORKDIR /app

RUN apt-get update && apt-get install -y \
    libdbus-1-3\
    libfontconfig1 \
    libgl1 \
    libegl1 \
    libglib2.0-0 \
    libxkbcommon0 \
    libxkbcommon-x11-0 \
    libxcb1 \
    libxcb-cursor0 \
    libxcb-xinerama0 \
    libxcb-randr0 \
    libxcb-shape0 \
    libxcb-xfixes0 \
    libsm6 \
    libxext6 \
    libxrender1 \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --upgrade pip setuptools wheel
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "Main.py"]
