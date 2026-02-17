# Compose_Physical_Buttons


https://github.com/user-attachments/assets/0d7482e2-fc3d-4f29-9396-408cbd42910f


Pressed3DButton
A Jetpack Compose button that sinks slightly when pressed, giving a realistic 3D effect. Combines vertical offset animation and layered colors for tactile feedback.


@Composable
fun Pressed3DButton() {
    // State to track if the button is currently being held down
    val interactionSource = remember { MutableInteractionSource() }
    val isPressed by interactionSource.collectIsPressedAsState()

    // Animate the vertical offset: 0dp when normal, 4dp when pressed
    val verticalOffset by animateDpAsState(
        targetValue = if (isPressed) 4.dp else 0.dp,
        label = "pressAnimation"
    )

    // The Container (The Shadow/Depth)
    Box(
        modifier = Modifier
            .padding(16.dp)
            .width(300.dp)
            .height(60.dp)
            .background(
                color = Color(0xFFC68B00), // Darker orange for the depth
                shape = RoundedCornerShape(20.dp)
            )
    ) {
        // The Top Layer (The actual button surface)
        Box(
            modifier = Modifier
                .fillMaxSize()
                .graphicsLayer {
                    // Move the button surface down when pressed
                    translationY = verticalOffset.toPx()
                }
                .background(
                    color = Color(0xFFF2A900), // Bright orange from your image
                    shape = RoundedCornerShape(20.dp)
                )
                .clickable(
                    interactionSource = interactionSource,
                    indication = null, // Disable default ripple for a cleaner 3D look
                    onClick = { /* Handle click */ }
                ),
            contentAlignment = Alignment.Center
        ) {
            Text(
                text = "CLICK ME",
                color = Color.White,
                fontWeight = FontWeight.Bold
            )
        }
    }
}

ExpandedPressButton
An interactive button that visibly collapses into its base when pressed. Uses spring-based animation for a smooth, natural push-down effect.


@Composable
fun ExpandedPressButton() {
    val interactionSource = remember { MutableInteractionSource() }
    val isPressed by interactionSource.collectIsPressedAsState()

    // The button "travel" distance (how deep it sinks)
    val depth = 6.dp

    // When NOT pressed, it's at 0 (raised). When pressed, it moves down by 'depth'.
    val verticalOffset by animateDpAsState(
        targetValue = if (isPressed) depth else 0.dp,
        animationSpec = spring(stiffness = Spring.StiffnessLow),
        label = "collapseAnimation"
    )

    // 1. The Shadow/Base (The part that stays still)
    Box(
        modifier = Modifier
            .padding(top = depth) // Offset the whole thing so the "pop" has room
            .width(300.dp)
            .height(70.dp)
            .background(
                color = Color(0xFFC68B00), // The darker "bottom" side
                shape = RoundedCornerShape(20.dp)
            )
    ) {
        // 2. The Interactive Top (The part that moves)
        Box(
            modifier = Modifier
                .offset(y = -depth) // Start it "floating" above the base
                .fillMaxSize()
                .graphicsLayer {
                    // This creates the collapse effect
                    translationY = verticalOffset.toPx()
                }
                .background(
                    color = Color(0xFFF2A900), // Main button color
                    shape = RoundedCornerShape(20.dp)
                )
                .clickable(
                    interactionSource = interactionSource,
                    indication = null, // Custom 3D buttons usually look better without ripples
                    onClick = { /* Action here */ }
                ),
            contentAlignment = Alignment.Center
        ) {
            Text(
                text = "PUSH DOWN",
                color = Color.White,
                style = MaterialTheme.typography.labelLarge.copy(fontWeight = FontWeight.ExtraBold)
            )
        }
    }
}

GlassGlowButton
A semi-transparent, glass-style button with a subtle glowing effect. Scales down slightly on press for interactive feedback.

@Composable
fun GlassGlowButton() {
    val interactionSource = remember { MutableInteractionSource() }
    val isPressed by interactionSource.collectIsPressedAsState()
    val scale by animateFloatAsState(if (isPressed) 0.95f else 1f, label = "scale")

    Box(
        modifier = Modifier
            .graphicsLayer {
                scaleX = scale
                scaleY = scale
            }
            .clip(RoundedCornerShape(24.dp))
            .background(Brush.linearGradient(listOf(Color.White.copy(0.2f), Color.White.copy(0.05f))))
            .border(1.dp, Color.White.copy(0.3f), RoundedCornerShape(24.dp))
            .clickable(interactionSource = interactionSource, indication = ripple(bounded = true, color = Color.White)) { }
            .padding(horizontal = 32.dp, vertical = 16.dp),
        contentAlignment = Alignment.Center
    ) {
        Text("GLASS GLOW", color = Color.White, fontWeight = FontWeight.Bold)
    }
}

LiquidPulseButton
A circular button with continuously animated gradient pulses. Creates a fluid, liquid-like visual effect that draws attention.


@Composable
fun LiquidPulseButton() {
    val infiniteTransition = rememberInfiniteTransition(label = "gradient")
    val offset by infiniteTransition.animateFloat(
        initialValue = 0f,
        targetValue = 1000f,
        animationSpec = infiniteRepeatable(
            animation = tween(3000, easing = LinearEasing),
            repeatMode = RepeatMode.Restart
        ), label = "offset"
    )

    val gradient = Brush.linearGradient(
        colors = listOf(Color(0xFF8E2DE2), Color(0xFF4A00E0), Color(0xFF8E2DE2)),
        start = Offset(offset, offset),
        end = Offset(offset + 500f, offset + 500f),
        tileMode = TileMode.Repeated
    )

    Button(
        onClick = { },
        shape = CircleShape,
        colors = ButtonDefaults.buttonColors(containerColor = Color.Transparent),
        contentPadding = PaddingValues(0.dp),
        modifier = Modifier.height(55.dp).width(200.dp)
    ) {
        Box(
            modifier = Modifier.fillMaxSize().background(gradient),
            contentAlignment = Alignment.Center
        ) {
            Text("LIQUID PULSE", color = Color.White, fontWeight = FontWeight.ExtraBold)
        }
    }
}

NeumorphicSnapButton
A compact neumorphic button that visually indents on press. Adjusts shadow and scale to simulate physical pressure.


@Composable
fun NeumorphicSnapButton() {
    val interactionSource = remember { MutableInteractionSource() }
    val isPressed by interactionSource.collectIsPressedAsState()

    // Animate the shadow elevation and corner "indent"
    val shadowColor = if (isPressed) Color.Black.copy(0.1f) else Color.Black.copy(0.3f)

    Box(
        modifier = Modifier
            .size(100.dp)
            .graphicsLayer {
                // Slight shrink to simulate physical pressure
                scaleX = if (isPressed) 0.97f else 1f
                scaleY = if (isPressed) 0.97f else 1f
            }
            .shadow(
                elevation = if (isPressed) 0.dp else 10.dp,
                shape = RoundedCornerShape(20.dp),
                ambientColor = shadowColor,
                spotColor = shadowColor
            )
            .background(Color(0xFFE0E0E0), RoundedCornerShape(20.dp))
            .clickable(interactionSource = interactionSource, indication = null) { },
        contentAlignment = Alignment.Center
    ) {
        Icon(
            imageVector = Icons.Filled.AccountBox,
            contentDescription = null,
            tint = if (isPressed) Color(0xFF00C853) else Color.Gray,
            modifier = Modifier.size(40.dp)
        )
    }
}

GlossyButton
A rounded button with gradient and gloss highlights for a polished appearance. Scales and shadows adjust dynamically on press.


@Composable
fun GlossyButton() {
    val interactionSource = remember { MutableInteractionSource() }
    val isPressed by interactionSource.collectIsPressedAsState()
    val scale by animateFloatAsState(if (isPressed) 0.96f else 1f)

    Box(
        modifier = Modifier
            .size(width = 220.dp, height = 60.dp)
            .graphicsLayer { scaleX = scale; scaleY = scale }
            .shadow(if (isPressed) 2.dp else 10.dp, RoundedCornerShape(50))
            .background(
                brush = Brush.verticalGradient(
                    colors = listOf(Color(0xFF42A5F5), Color(0xFF1976D2))
                ),
                shape = RoundedCornerShape(50)
            )
            .clickable(interactionSource = interactionSource, indication = ripple()) {}
    ) {
        // The "Gloss" Highlight
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .fillMaxHeight(0.5f)
                .padding(horizontal = 12.dp, vertical = 4.dp)
                .background(
                    brush = Brush.verticalGradient(
                        colors = listOf(Color.White.copy(alpha = 0.4f), Color.Transparent)
                    ),
                    shape = RoundedCornerShape(50)
                )
        )
        Text(
            "GLOSSY",
            modifier = Modifier.align(Alignment.Center),
            color = Color.White,
            style = TextStyle(fontWeight = FontWeight.Bold, shadow = Shadow(blurRadius = 4f))
        )
    }
}

MetalButton
A metallic circular button with layered gradients and inner rings for realism. Pressing modifies shadow to enhance depth perception.


@Composable
fun MetalButton() {
    val interactionSource = remember { MutableInteractionSource() }
    val isPressed by interactionSource.collectIsPressedAsState()

    val metalColors = listOf(
        Color(0xFFB8B8B8), Color(0xFFEFEFEF),
        Color(0xFFB8B8B8), Color(0xFF707070),
        Color(0xFFB8B8B8)
    )

    Box(
        modifier = Modifier
            .size(100.dp)
            .border(2.dp, Color.DarkGray, CircleShape)
            .shadow(if (isPressed) 1.dp else 6.dp, CircleShape)
            .background(Brush.sweepGradient(metalColors), CircleShape)
            .clickable(interactionSource = interactionSource, indication = ripple(bounded = true)) {},
        contentAlignment = Alignment.Center
    ) {
        // Inner ring for extra realism
        Box(
            modifier = Modifier
                .fillMaxSize(0.8f)
                .border(1.dp, Color.White.copy(0.3f), CircleShape)
        )
        Icon(Icons.Default.Settings, contentDescription = null, tint = Color(0xFF444444))
    }
}

SoftPlasticButton
A soft, plastic-like button with light and dark shadowing for depth. Pressing flattens the shadows to simulate compressible material.


@Composable
fun SoftPlasticButton() {
    val interactionSource = remember { MutableInteractionSource() }
    val isPressed by interactionSource.collectIsPressedAsState()
    val bgColor = Color(0xFFE0E5EC)

    Box(
        modifier = Modifier
            .size(120.dp)
            // Light shadow (top-left)
            .drawBehind {
                if (!isPressed) {
                    drawIntoCanvas { canvas ->
                        val paint = Paint().asFrameworkPaint().apply {
                            setShadowLayer(15.dp.toPx(), -0.dp.toPx(), -0.dp.toPx(), android.graphics.Color.WHITE)
                        }
                        canvas.nativeCanvas.drawRoundRect(0f, 0f, size.width, size.height, 40f, 40f, paint)
                    }
                }
            }
            // Dark shadow (bottom-right)
            .shadow(
                elevation = if (isPressed) 0.dp else 10.dp,
                shape = RoundedCornerShape(20.dp),
                ambientColor = Color.Black.copy(0.2f),
                spotColor = Color.Black.copy(0.2f)
            )
            .background(bgColor, RoundedCornerShape(20.dp))
            .clickable(interactionSource = interactionSource, indication = null) {},
        contentAlignment = Alignment.Center
    ) {
        Text("PRESS", color = Color.Gray.copy(0.7f), fontWeight = FontWeight.Bold)
    }
}

