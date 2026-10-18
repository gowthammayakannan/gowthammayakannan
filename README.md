#!/bin/bash

# ==========================================
# 🚀 TalkBetter Development Startup Script
# ==========================================

# Colors
GREEN='\033[0;32m'
BLUE='\033[0;34m'
PURPLE='\033[0;35m'
NC='\033[0m' # No Color

echo -e "${PURPLE}========================================${NC}"
echo -e "${PURPLE}   TalkBetter - Communication Platform   ${NC}"
echo -e "${PURPLE}========================================${NC}"
echo ""

# Function to check and install dependencies
check_deps() {
    DIR=$1
    NAME=$2
    if [ ! -d "$DIR/node_modules" ]; then
        echo -e "${BLUE}📦 Installing $NAME dependencies...${NC}"
        cd $DIR
        npm install
        cd ..
        echo -e "${GREEN}✅ $NAME dependencies installed.${NC}"
    else
        echo -e "${GREEN}✅ $NAME dependencies ready.${NC}"
    fi
}

# Check all layers
check_deps "." "Root"
check_deps "frontend" "Frontend"
check_deps "backend" "Backend"

# Check for LiveKit Server
LIVEKIT_CMD=""
LIVEKIT_NAME=""
LIVEKIT_COLOR=""

if command -v livekit-server &> /dev/null; then
    echo -e "${GREEN}✅ LiveKit Server found. Adding to startup...${NC}"
    LIVEKIT_CMD="\"livekit-server --config livekit.yaml --dev\""
    LIVEKIT_NAME=",LIVEKIT"
    LIVEKIT_COLOR=",yellow"
else
    echo -e "${PURPLE}⚠️  livekit-server binary not found in PATH.${NC}"
    echo -e "${PURPLE}   Running without local media server. (Install LiveKit for full features)${NC}"
fi

echo ""
echo -e "${BLUE}🚀 Starting Application Services...${NC}"
echo "----------------------------------------"
echo -e "   Frontend: ${GREEN}http://localhost:5173${NC}"
echo -e "   Backend:  ${GREEN}http://localhost:5000${NC}"
if [ -n "$LIVEKIT_CMD" ]; then
    echo -e "   LiveKit:  ${GREEN}http://localhost:7880${NC}"
fi
echo "----------------------------------------"
echo "Press Ctrl+C to stop all services."
echo ""

# Build the command
CMD="./node_modules/.bin/concurrently --names \"FRONTEND,BACKEND$LIVEKIT_NAME\" --prefix-colors \"cyan,magenta$LIVEKIT_COLOR\" --kill-others \"npm run dev:frontend\" \"npm run dev:backend\""

if [ -n "$LIVEKIT_CMD" ]; then
    CMD="$CMD $LIVEKIT_CMD"
fi

# Execute
eval "$CMD"
