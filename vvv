import SwiftUI

struct Brick: Identifiable {
    let id = UUID()
    var rect: CGRect
    var isHit = false
}

struct BrickBreakingGame: View {
    @State private var ballPosition = CGPoint(x: 200, y: 400)
    @State private var ballDirection = CGSize(width: 1, height: -1)
    @State private var paddlePosition = CGFloat(200)
    @State private var bricks: [Brick] = []
    let ballSize: CGFloat = 20
    let paddleWidth: CGFloat = 100
    let paddleHeight: CGFloat = 20
    
    var body: some View {
        GeometryReader { geometry in
            ZStack {
                // 砖块
                ForEach(bricks) { brick in
                    if !brick.isHit {
                        Rectangle()
                            .frame(width: brick.rect.width, height: brick.rect.height)
                            .position(x: brick.rect.midX, y: brick.rect.midY)
                            .foregroundColor(.orange)
                    }
                }
                
                // 球
                Circle()
                    .frame(width: ballSize, height: ballSize)
                    .position(x: ballPosition.x, y: ballPosition.y)
                    .foregroundColor(.blue)
                    .onAppear {
                        setupBricks(geometry.size)
                        startGameLoop(in: geometry.size)
                    }
                
                // 挡板
                Rectangle()
                    .frame(width: paddleWidth, height: paddleHeight)
                    .position(x: paddlePosition, y: geometry.size.height - paddleHeight)
                    .foregroundColor(.gray)
                    .gesture(
                        DragGesture().onChanged { value in
                            paddlePosition = value.location.x
                        }
                    )
            }
        }
    }
    
    private func setupBricks(_ size: CGSize) {
        let rows = 5
        let columns = 6
        let brickWidth = size.width / CGFloat(columns)
        let brickHeight: CGFloat = 30
        
        bricks = (0..<rows).flatMap { row in
            (0..<columns).map { column in
                let x = brickWidth * CGFloat(column) + brickWidth / 2
                let y = brickHeight * CGFloat(row) + brickHeight / 2 + 50
                return Brick(rect: CGRect(x: x, y: y, width: brickWidth - 5, height: brickHeight - 5))
            }
        }
    }
    
    private func startGameLoop(in size: CGSize) {
        Timer.scheduledTimer(withTimeInterval: 0.01, repeats: true) { _ in
            moveBall(in: size)
        }
    }
    
    private func moveBall(in size: CGSize) {
        ballPosition.x += ballDirection.width * 4
        ballPosition.y += ballDirection.height * 4
        
        // 球与挡板的碰撞
        if ballPosition.y + ballSize / 2 >= size.height - paddleHeight &&
            ballPosition.x > paddlePosition - paddleWidth / 2 &&
            ballPosition.x < paddlePosition + paddleWidth / 2 {
            ballDirection.height = -ballDirection.height
        }
        
        // 球与墙壁的碰撞
        if ballPosition.x <= ballSize / 2 || ballPosition.x >= size.width - ballSize / 2 {
            ballDirection.width = -ballDirection.width
        }
        if ballPosition.y <= ballSize / 2 {
            ballDirection.height = -ballDirection.height
        }
        
        // 球与砖块的碰撞
        for index in bricks.indices {
            if !bricks[index].isHit && bricks[index].rect.contains(ballPosition) {
                bricks[index].isHit = true
                ballDirection.height = -ballDirection.height
                break
            }
        }
        
        // 球掉到屏幕底部
        if ballPosition.y >= size.height {
            resetGame(size: size)
        }
    }
    
    private func resetGame(size: CGSize) {
        ballPosition = CGPoint(x: size.width / 2, y: size.height / 2)
        ballDirection = CGSize(width: 1, height: -1)
        setupBricks(size)
    }
}

struct BrickBreakingGame_Previews: PreviewProvider {
    static var previews: some View {
        BrickBreakingGame()
    }
}
