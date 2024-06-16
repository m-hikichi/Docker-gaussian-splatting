FROM node:lts

WORKDIR /workspace
RUN git clone https://github.com/playcanvas/super-splat.git -b v0.19.2

WORKDIR /workspace/super-splat
RUN npm install
CMD ["npm", "run", "develop"]
