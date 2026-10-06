FROM node:24-slim

WORKDIR /app

COPY . .

RUN npm install

EXPOSE 5173

# Use exec form for proper signal handling
ENTRYPOINT ["node", "--no-warnings"]
CMD ["./node_modules/.bin/vite", "preview", "--host", "0.0.0.0", "--port", "4173"]
 