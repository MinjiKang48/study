## Seq2Seq(RNN)

#### encoder

- seq2seq 네트워크의 인코더는 입력 문장에서 모든 단어의 값에 대한 어떠한 값을 출력으로 하는 RNN
- 모든 입력 단어에 대해 인코더는 하나의 벡터와 hidden state를 출력하고, hidden state를 다음 입력  단어를 위해 사용한다.

#### decoder

- 인코더의 출력 벡터를 가지고 번역된 단어들로 이루어진 한 문장을 출력하는 또다른 RNN

1. Simple Decoder
   - 제일 간단한 seq2seq 디코더는 인코더에서 나온 마지막 출력을 사용하는 것이다.
   - 마지막 출력은 *context vector*라고도 한다. 전체 시퀀스에서부터 문맥을 인코딩한 것이라서 이렇게도 불린다
   - context vector는 디코더의 초기 hidden state로 사용되기도 한다.
   - 디코딩하는 모든 스텝마다, 디코더에 입력 토큰과 hidden state가 주어진다.
   - 초기 입력 토큰은 <SOS> start-of-string 이고, 초기 hidden state는 인코더의 마지막 hidden state인 context vector이다.
2. Attention Decoder
   - context vector만 encoder와 decoder 사이를 지났다면, that single vector carries the burden of encoding the entire sentence.
   - 'Attention'은 de

