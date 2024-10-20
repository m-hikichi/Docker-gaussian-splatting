FROM node:lts

WORKDIR /workspace
RUN git clone https://github.com/playcanvas/super-splat.git -b v1.6.0

WORKDIR /workspace/super-splat
RUN npm install
CMD ["npm", "run", "develop"]
