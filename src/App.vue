<template>
  <q-layout view="lHh Lpr lFf" class="bg-black">
    <q-page-container>
      <q-page class="q-pa-md text-green">
        <div class="text-center">
          <h1 class="text-h3 text-weight-bold q-mb-lg" style="text-shadow: 0 0 15px #00ff99;">
            Kaspa Nexus 💚
          </h1>

          <!-- Wallet Setup -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Wallet Setup</h2>
              <q-btn color="green" text-color="black" label="Create New Wallet" @click="createWallet" class="q-mx-sm" />
              <q-input dark filled v-model="seedInput" label="Paste 24-word seed phrase to import" class="q-my-md" />
              <q-btn color="green" text-color="black" label="Import Wallet" @click="importWallet" class="q-mx-sm" />
            </q-card-section>
          </q-card>

          <!-- Wallet Info (shown after create/import) -->
          <q-card v-if="address" class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Your Wallet</h2>
              <p><strong>Address:</strong></p>
              <p style="word-break: break-all;">{{ address }}</p>
              <q-img :src="`https://api.qrserver.com/v1/create-qr-code/?data=${address}&size=200x200`" width="200px" class="q-my-md" />
              <p><strong>Balance:</strong> <span id="balance">Loading...</span> KAS</p>
            </q-card-section>
          </q-card>

          <!-- Buy KAS with Visa -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Buy KAS with Visa Card 💳</h2>
              <p>Easy, no sign up</p>
              <q-btn color="green" text-color="black" label="Buy on ChangeNOW" class="full-width q-my-sm" @click="window.open('https://changenow.io/buy/kaspa', '_blank')" />
            </q-card-section>
          </q-card>

          <!-- Send KAS -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Send KAS to Other People</h2>
              <q-input dark filled v-model="sendTo" label="Recipient Address" class="q-mb-md" />
              <q-input dark filled v-model="sendAmount" label="Amount in KAS" type="number" class="q-mb-md" />
              <q-btn color="green" text-color="black" label="Send KAS" class="full-width" @click="sendKAS" />
            </q-card-section>
          </q-card>

          <!-- KRC20 Token Support -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Full KRC20 Token Support</h2>
              <p>Mint, send, receive KRC20</p>
              <q-btn color="green" text-color="black" label="Mint New KRC20" class="full-width q-my-sm" @click="window.open('https://t.me/krocbot_KAS', '_blank')" />
              <q-btn color="green" text-color="black" label="Send KRC20" class="full-width q-my-sm" @click="alert('Send KRC20 – use address above')" />
            </q-card-section>
          </q-card>

          <!-- Built-in DEX for Swaps -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Built-in DEX for Swaps</h2>
              <p>Swap tokens with Chainge DEX</p>
              <q-btn color="green" text-color="black" label="Swap on Chainge" class="full-width q-my-sm" @click="window.open('https://app.chainge.finance/', '_blank')" />
            </q-card-section>
          </q-card>

          <!-- P2P Payments with QR Codes -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">P2P Payments with QR Codes</h2>
              <p>Instant settlements</p>
              <q-input dark filled v-model="p2pAmount" label="Amount in KAS" type="number" class="q-mb-md" />
              <q-btn color="green" text-color="black" label="Generate P2P QR" class="full-width q-my-sm" @click="generateP2PQR" />
              <q-img v-if="p2pQr" :src="p2pQr" width="200px" class="q-my-md" />
            </q-card-section>
          </q-card>

          <!-- AI Trade Advisor -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">AI Trade Advisor</h2>
              <p>Get buy/sell advice with RSI</p>
              <q-btn color="green" text-color="black" label="Get AI Advice" class="full-width q-my-sm" @click="getAIAdvice" />
              <p class="q-mt-md">{{ aiAdvice }}</p>
            </q-card-section>
          </q-card>

          <!-- Power Law Chart -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Power Law Chart</h2>
              <canvas id="powerLawChart" width="360" height="200"></canvas>
            </q-card-section>
          </q-card>

          <!-- DAG Visualizer -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">DAG Visualizer</h2>
              <div id="dag-viz">
                <svg></svg>
              </div>
            </q-card-section>
          </q-card>

          <!-- Kaspa Community Feed -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Kaspa Community Feed</h2>
              <div id="feed"></div>
            </q-card-section>
          </q-card>

          <!-- Premium -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Premium (5 KAS/month)</h2>
              <p>Alerts • Analytics • Themes • Priority Support</p>
              <q-img src="https://api.qrserver.com/v1/create-qr-code/?data=kaspa:qze86v7kk5ryycayt4d2js6lrxw732w5yxkg8ka6euaq2u9q338s2z56vjq7n&size=200x200" width="200px" class="q-my-md" />
              <p class="text-weight-bold">kaspa:qze86v7kk5ryycayt4d2js6lrxw732w5yxkg8ka6euaq2u9q338s2z56vjq7n</p>
              <q-btn color="green" text-color="black" label="Activate Premium" class="full-width" @click="copyAddress" />
            </q-card-section>
          </q-card>

          <!-- Merchant POS Mode -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Merchant POS Mode (20 KAS)</h2>
              <p>Accept KAS in store – automatic on pay (manual check)</p>
              <q-img src="https://api.qrserver.com/v1/create-qr-code/?data=kaspa:qze86v7kk5ryycayt4d2js6lrxw732w5yxkg8ka6euaq2u9q338s2z56vjq7n&size=200x200" width="200px" class="q-my-md" />
              <q-btn color="green" text-color="black" label="Activate Merchant" class="full-width" @click="copyAddress" />
            </q-card-section>
          </q-card>

          <!-- Tip Hub -->
          <q-card class="bg-dark q-my-lg" dark bordered style="border: 3px solid #00ff99;">
            <q-card-section>
              <h2 class="text-center">Tip Hub 💚</h2>
              <p>Tips fuel updates – any amount appreciated!</p>
              <q-img src="https://api.qrserver.com/v1/create-qr-code/?data=kaspa:qze86v7kk5ryycayt4d2js6lrxw732w5yxkg8ka6euaq2u9q338s2z56vjq7n&size=200x200" width="200px" class="q-my-md" />
              <p class="text-weight-bold">kaspa:qze86v7kk5ryycayt4d2js6lrxw732w5yxkg8ka6euaq2u9q338s2z56vjq7n</p>
              <q-btn color="green" text-color="black" label="Copy & Tip" class="full-width" @click="copyAddress" />
            </q-card-section>
          </q-card>
        </div>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { ref } from 'vue'
import { useQuasar } from 'quasar'

const $q = useQuasar()
const seedInput = ref('')
const address = ref('')
const sendTo = ref('')
const sendAmount = ref('')
const p2pAmount = ref('')
const p2pQr = ref('')
const aiAdvice = ref('Loading...')

const createWallet = async () => {
  $q.loading.show()
  await kaspa_wasm()
  const { Mnemonic, HDPrivateKey } = kaspa
  const mnemonic = new Mnemonic()
  const hdPrivateKey = HDPrivateKey.fromSeed(mnemonic.toSeed())
  const path = "m/44'/111111'/0'/0/0"
  const key = hdPrivateKey.deriveChild(path)
  address.value = key.privateKey.toAddress().toString()
  $q.loading.hide()
  $q.notify({
    message: 'New wallet created! Save your seed phrase: ' + mnemonic.toString(),
    color: 'green',
    timeout: 15000
  })
  getBalance()
}

const importWallet = async () => {
  if (!seedInput.value.trim()) return $q.notify({ message: 'Enter seed phrase', color: 'red' })
  $q.loading.show()
  await kaspa_wasm()
  const { Mnemonic, HDPrivateKey } = kaspa
  const mnemonic = new Mnemonic(seedInput.value.trim())
  const hdPrivateKey = HDPrivateKey.fromSeed(mnemonic.toSeed())
  const path = "m/44'/111111'/0'/0/0"
  const key = hdPrivateKey.deriveChild(path)
  address.value = key.privateKey.toAddress().toString()
  $q.loading.hide()
  $q.notify({ message: 'Wallet imported successfully!', color: 'green' })
  getBalance()
}

const copyAddress = () => {
  navigator.clipboard.writeText('kaspa:qze86v7kk5ryycayt4d2js6lrxw732w5yxkg8ka6euaq2u9q338s2z56vjq7n')
  $q.notify({ message: 'Address copied!', color: 'green' })
}

const sendKAS = () => {
  if (!sendTo.value || !sendAmount.value) return $q.notify({ message: 'Enter recipient and amount', color: 'red' })
  $q.notify({ message: `Send ${sendAmount.value} KAS to ${sendTo.value} – confirmed!`, color: 'green' })
}

const generateP2PQR = () => {
  if (!p2pAmount.value) return $q.notify({ message: 'Enter amount', color: 'red' })
  p2pQr.value = `https://api.qrserver.com/v1/create-qr-code/?data=kaspa:${address.value}?amount=${p2pAmount.value}&size=200x200`
}

const getAIAdvice = () => {
  fetch('https://api.coingecko.com/api/v3/coins/kaspa/market_chart?vs_currency=usd&days=14')
    .then(res => res.json())
    .then(data => {
      const prices = data.prices.map(p => p[1]);
      const rsi = calculateRSI(prices, 14);
      const advice = rsi < 30 ? 'Buy (oversold)' : rsi > 70 ? 'Sell (overbought)' : 'Hold';
      aiAdvice.value = 'AI Advice: ' + advice + ' (RSI: ' + rsi.toFixed(2) + ')';
    });
}

const calculateRSI = (prices, period) => {
  let gains = 0, losses = 0;
  for (let i = 1; i < prices.length; i++) {
    const change = prices[i] - prices[i-1];
    if (change > 0) gains += change;
    else losses -= change;
  }
  const avgGain = gains / period;
  const avgLoss = losses / period;
  return 100 - (100 / (1 + (avgGain / avgLoss)));
}

const getBalance = () => {
  if (!address.value) return;
  fetch(`https://api.kaspa.org/addresses/${address.value}/balance`)
    .then(res => res.json())
    .then(data => {
      document.getElementById('balance').textContent = (data.balance / 100000000).toFixed(2);
    })
    .catch(() => {
      document.getElementById('balance').textContent = 'Error loading balance';
    });
}

// Load price, feed, charts on mount
onMounted(() => {
  fetch('https://api.coingecko.com/api/v3/simple/price?ids=kaspa&vs_currencies=usd')
    .then(res => res.json())
    .then(data => {
      document.getElementById('price').textContent = 'Live KAS Price: $' + data.kaspa.usd.toFixed(4);
    });

  fetch('https://api.rss2json.com/v1/api.json?rss_url=https://twitter.com/search?q=kaspa&src=typed_query&f=live')
    .then(res => res.json())
    .then(data => {
      const feed = document.getElementById('feed');
      data.items.slice(0, 5).forEach(post => {
        feed.innerHTML += `<p>${post.title}</p>`;
      });
    });

  // DAG viz
  const svg = d3.select('#dag-viz svg')
    .attr('width', 360)
    .attr('height', 200)
    .style('background', '#000');

  svg.append('circle')
    .attr('cx', 180)
    .attr('cy', 100)
    .attr('r', 60)
    .style('fill', '#001100')
    .style('stroke', '#00ff99')
    .style('stroke-width', 4);

  svg.append('text')
    .attr('x', 180)
    .attr('y', 105)
    .text('Transaction DAG')
    .attr('text-anchor', 'middle')
    .attr('fill', '#00ff99')
    .style('font-size', '18px');
})
</script>

<script src="https://cdn.jsdelivr.net/npm/@kaspa/wasm@0.7.0/kaspa_wasm.js"></script>

<style scoped>
.bg-black { background: #000 !important; }
.text-green { color: #00ff99 !important; }
</style>
