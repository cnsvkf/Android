    실무에서는
    대부분 컴포넌트의 첫 번째 또는 마지막 파라미터로
    modifier: Modifier = Modifier
    를 추가합니다.

-> 그 이유는 부모가 다음을 제어할 수 있도록 하기 위해서입니다.

    크기 (size, width, height, fillMaxWidth)
    위치 (padding, offset, align)
    레이아웃 (weight)
    클릭 (clickable)
    애니메이션 (graphicsLayer)
    배경 (background)
    테두리 (border)

-> 즉, Modifier는 부모가 자식의 레이아웃과 표시 방식을 제어하기 위한 통로입니다.