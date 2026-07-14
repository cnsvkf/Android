### buildAnnotatedString

| 구분        | `AnnotatedString` | `buildAnnotatedString` |
| --------- | ----------------- | ---------------------- |
| 종류        | 클래스, 자료형          | 함수                     |
| 역할        | 완성된 텍스트를 저장       | `AnnotatedString`을 생성  |
| 스타일 범위    | 직접 계산하는 경우가 많음    | 자동 계산                  |
| 여러 스타일 적용 | 상대적으로 복잡함         | 편리함                    |
| 주 사용 상황   | 완성된 값 전달, 단순 문자열  | 여러 색상, 링크, 굵기 적용       |

-> 

    AnnotatedString
    └─ 완성된 결과의 자료형

    buildAnnotatedString
    └─ AnnotatedString을 편하게 만드는 함수

    append / withStyle / withLink
    └─ 만드는 과정에서 사용하는 Builder 함수


#### 안에서 쓰는 함수

| 목적        | 편한 방식                           | 수동 방식                              | 인덱스 직접 지정               |
| --------- | ------------------------------- | ---------------------------------- | ----------------------- |
| 텍스트 추가    | `append()`                      | —                                  | —                       |
| 글자 스타일    | `withStyle(SpanStyle)`          | `pushStyle()` + `pop()`            | `addStyle()`            |
| 문단 스타일    | `withStyle(ParagraphStyle)`     | `pushStyle()` + `pop()`            | `addStyle()`            |
| 클릭 링크     | `withLink()`                    | `pushLink()` + `pop()`             | `addLink()`             |
| 문자열 정보 저장 | `withAnnotation()`              | `pushStringAnnotation()` + `pop()` | `addStringAnnotation()` |
| 글머리 기호    | `withBulletList()`              | `pushBullet()` + `pop()`           | `addBullet()`           |
| 음성 읽기 정보  | `withAnnotation(TtsAnnotation)` | `pushTtsAnnotation()` + `pop()`    | `addTtsAnnotation()`    |
| 텍스트 내부 UI | `appendInlineContent()`         | —                                  | —                       |

- 한 문구에 특정 부분만 링크 추가
```kotlin
/**
 * 약관 문장 중
 * "개인정보 제3자 제공" 부분만 클릭 가능하게 만드는 Composable이다.
 *
 * 클릭하면 showDialog 상태가 true로 변경되고,
 * ThirdPartyAgreementDialog 팝업이 화면에 표시된다.
 *
 * @param modifier
 * 외부에서 이 Text에 padding, width, align 등의 Modifier를 적용하기 위한 매개변수이다.
 */
@Composable
fun ThirdPartyAgreementText(
    modifier: Modifier = Modifier,
) {
    /*
     * 팝업 표시 여부를 저장하는 상태이다.
     *
     * false:
     * 팝업을 표시하지 않는다.
     *
     * true:
     * 개인정보 제공 안내 팝업을 표시한다.
     *
     * rememberSaveable을 사용했기 때문에
     * 화면 재구성뿐만 아니라 화면 회전 같은 상태 복원 상황에서도
     * 값을 가능한 한 유지한다.
     *
     * by를 사용했기 때문에
     * showDialog.value 대신 showDialog처럼 직접 사용할 수 있다.
     */
    var showDialog by rememberSaveable {
        mutableStateOf(false)
    }

    /*
     * 여러 스타일과 클릭 영역을 포함할 수 있는
     * AnnotatedString을 생성한다.
     *
     * 일반 String과 달리
     * 특정 텍스트 범위에 색상, 굵기, 링크 등의 정보를 넣을 수 있다.
     */
    val agreementText = buildAnnotatedString {
        /*
         * append()
         *
         * 현재 AnnotatedString 뒤에 일반 문자열을 추가한다.
         *
         * 이 영역은 withLink 밖에 있으므로 클릭되지 않는다.
         */
        append("독서마라톤 계정 연동을 위한 ")

        /*
         * withLink()
         *
         * 이 블록 안에서 추가되는 텍스트에
         * 클릭 가능한 링크 정보를 적용한다.
         *
         * 여기서는 "개인정보 제3자 제공" 텍스트만
         * 클릭 가능한 영역이 된다.
         */
        withLink(
            /*
             * LinkAnnotation.Clickable
             *
             * 웹 주소로 이동하는 URL 링크가 아니라
             * 클릭했을 때 원하는 코드를 실행하는 링크이다.
             */
            link = LinkAnnotation.Clickable(
                /*
                 * 링크를 구분하기 위한 식별자이다.
                 *
                 * 현재 링크가 하나뿐이라 직접 사용하지 않지만,
                 * 여러 개의 링크를 구분할 때 사용할 수 있다.
                 */
                tag = "third_party_agreement",

                /*
                 * 링크 텍스트에 적용할 스타일을 설정한다.
                 */
                styles = TextLinkStyles(
                    /*
                     * 기본 상태의 링크 스타일이다.
                     */
                    style = SpanStyle(
                        // 링크 텍스트 색상을 메인 초록색으로 설정한다.
                        color = BookOnColor.Primary,

                        // 링크 텍스트 굵기를 SemiBold로 설정한다.
                        fontWeight = FontWeight.SemiBold,
                    ),
                ),

                /*
                 * 링크 영역을 클릭했을 때 실행되는 콜백이다.
                 *
                 * showDialog를 true로 변경하면
                 * 아래의 if (showDialog) 조건이 참이 되어
                 * 팝업이 화면에 표시된다.
                 */
                linkInteractionListener = {
                    showDialog = true
                },
            ),
        ) {
            /*
             * withLink 블록 내부에서 추가했으므로
             * 이 텍스트만 클릭 가능한 영역이 된다.
             */
            append("개인정보 제3자 제공")
        }

        /*
         * withLink 블록이 끝난 뒤 추가했으므로
         * 이 텍스트는 클릭되지 않는다.
         */
        append("에 동의합니다.")
    }

    Text(

        text = agreementText,

        modifier = modifier,

        style = MaterialTheme.typography.bodyMedium,

        color = BookOnColor.TextSecondary,
    )


    if (showDialog) {
        ThirdPartyAgreementDialog(
            /*
             * 팝업을 닫아야 할 때 호출되는 함수이다.
             *
             * showDialog를 false로 변경하면
             * 다음 재구성에서 AlertDialog가 제거된다.
             */
            onDismissRequest = {
                showDialog = false
            },
        )
    }
}

/**
 * 개인정보 제3자 제공 내용을 표시하는 팝업 Composable이다.
 *
 * 이 함수는 현재 파일 내부에서만 사용하므로 private으로 선언했다.
 *
 * 이 함수 자체는 팝업 표시 상태를 저장하지 않는다.
 * 팝업을 닫아야 할 때 실행할 함수를 외부에서 전달받는다.
 *
 * @param onDismissRequest
 * 팝업 바깥을 클릭하거나 확인 버튼을 눌렀을 때
 * 실행할 닫기 콜백이다.
 */
@Composable
private fun ThirdPartyAgreementDialog(
    onDismissRequest: () -> Unit,
) {
    /*
     * Material3에서 제공하는 기본 팝업 UI이다.
     *
     * 제목, 본문, 확인 버튼 등을 슬롯 방식으로 전달한다.
     */
    AlertDialog(
        /*
         * 사용자가 팝업 바깥 영역을 누르거나
         * 시스템 뒤로가기를 실행했을 때 호출된다.
         *
         * 상위 함수에서 전달한 onDismissRequest가 실행된다.
         */
        onDismissRequest = onDismissRequest,

        /*
         * title 매개변수는
         * 팝업 제목 영역에 표시할 Composable을 받는다.
         */
        title = {
            Text(
                text = "개인정보 제3자 제공 안내",
            )
        },

        /*
         * text 매개변수는
         * 팝업 본문 영역에 표시할 Composable을 받는다.
         */
        text = {
            Text(
                /*
                 * 큰따옴표 3개를 사용하면
                 * 여러 줄 문자열을 작성할 수 있다.
                 */
                text = """
                    제공받는 자: 독서마라톤 서비스
                    
                    제공 목적: 독서마라톤 계정 연동
                    
                    제공 항목: 사용자 식별 정보, 계정 연동 정보
                    
                    보유 기간: 계정 연동 해제 또는 회원 탈퇴 시까지
                """.trimIndent(),
                /*
                 * trimIndent()
                 *
                 * 코드 정렬을 위해 문자열 앞에 넣은 공통 들여쓰기를 제거한다.
                 *
                 * 이 메서드가 없으면 팝업 본문의 각 줄 앞에
                 * 불필요한 공백이 들어갈 수 있다.
                 */
            )
        },

        /*
         * confirmButton은
         * 팝업의 확인 버튼 영역에 표시할 Composable을 받는다.
         */
        confirmButton = {
            TextButton(
                /*
                 * 확인 버튼을 클릭하면
                 * 전달받은 닫기 콜백을 실행한다.
                 *
                 * 결과적으로 showDialog가 false가 되어
                 * 팝업이 닫힌다.
                 */
                onClick = onDismissRequest,
            ) {
                Text(
                    text = "확인",
                )
            }
        },
    )
}
```