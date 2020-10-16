<template>
  <div>
    <div v-if="filteredResults.length > 0">
      <TokenDisplay
        v-for="value in filteredResults"
        :key="value.contract || value.id"
        :tokenData="value"
      />
    </div>
    <div v-else class="tokens-msg">{{ $t('pages.fungible-tokens.no-results') }}</div>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex';
import TokenDisplay from './TokenDisplay';
import { removeDuplicates } from '../../../utils/helper';

export default {
  components: {
    TokenDisplay,
  },
  props: {
    showMyTokens: { type: Boolean },
    searchTerm: { type: String, default: '' },
  },
  computed: {
    ...mapState('fungibleTokens', ['tokenBalances', 'tokenInfo', 'aeTokenInfo']),
    ...mapGetters(['tokenBalance', 'balanceCurrency']),

    /**
     * Returns the default aeternity meta information
     */
    aeternityToken() {
      const aeInformation =
        this.aeTokenInfo && Object.keys(this.aeTokenInfo).length > 0
          ? {
              ...this.aeTokenInfo,
              convertedBalance: this.tokenBalance,
              symbol: 'AE',
              balanceCurrency: this.balanceCurrency,
              contract: '',
            }
          : null;
      return aeInformation;
    },
    /**
     * Converts the token information object into a searchable list
     */
    convertedTokenInfo() {
      return Object.entries(this.tokenInfo).map(([contract, tokenData]) => ({
        name: tokenData.name,
        symbol: tokenData.symbol,
        contract,
        decimals: tokenData.decimals,
        convertedBalance: tokenData.convertedBalance,
      }));
    },
    tokensInfo() {
      if (this.aeternityToken && this.convertedTokenInfo.length > 0) {
        return [{ ...this.aeternityToken }, ...this.convertedTokenInfo];
      }
      if (this.convertedTokenInfo.length > 0) {
        return [...this.convertedTokenInfo];
      }
      if (this.aeternityToken) {
        return [{ ...this.aeternityToken }];
      }

      return [];
    },
    filteredResults() {
      let tokensList = null;

      tokensList = this.tokensInfo;

      if (this.showMyTokens) {
        if (!this.aeternityToken && this.tokenBalances.length === 0) {
          return [];
        }
        tokensList = this.aeternityToken ? [{ ...this.aeternityToken }] : [];
        tokensList =
          this.tokenBalances.length > 0 ? [...tokensList, ...this.tokenBalances] : tokensList;
      }

      if (this.searchTerm.trim().length === 0) {
        return removeDuplicates(tokensList);
      }

      const term = this.searchTerm.trim().toLowerCase();

      const searchResults = tokensList.filter(
        token =>
          token.symbol.toLowerCase().includes(term) ||
          token.name.toLowerCase().includes(term) ||
          token.contract.toLowerCase().includes(term),
      );
      return removeDuplicates([...searchResults]);
    },
  },
};
</script>

<style lang="scss" scoped>
.tokens-msg {
  text-align: center;
  margin-top: 15px;
}
</style>
