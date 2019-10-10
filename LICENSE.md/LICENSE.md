pragma solidity 0.5.12;

contract Emprestimo
{
    
    string public mutuante;
    string public mutuario;
    uint256 private valorbase;
    uint256 private valor;
    uint256 private prazo;
    // Juros mantidos conforme o padrão estabelecido em lei, de 1% ao mês para contratos com prazo de 12 meses.
    uint256 constant percentualjuros = 1;
    
    constructor (string memory nomeMutuante, string memory nomeMutuario, uint256 valorBaseMutuo, uint256 prazoParaPagamento) public 
    {
        mutuante = nomeMutuante;
        mutuario = nomeMutuario;
        valorbase = valorBaseMutuo;
        prazo = prazoParaPagamento;
    }
    
    
    
    function mostraValorAtualizado () public view returns (uint256)
    {
        if (valor <= 0)
        {
            return valorbase;
        }
        
        return valor;
    }

    
    function atualizaValorMutuo (uint256 mesesDecorridos, uint256 multa) public returns (uint256 valorAtual) 
    {
        // Atualização do valor, pela modalidade de juros simples.
        valorAtual = (valorbase*percentualjuros)/100;
        valorAtual = valorAtual*mesesDecorridos;
        valorAtual = valorAtual + valorbase;
        
        if (mesesDecorridos > prazo)
        {
            // Limitar o valor da multa a 10%, pois é o máximo permitido em lei.   
            if (multa > 10)
            {
                multa = 10;
            }
            
            // A modalidade de juros moratórios será de juros compostos.
            for (uint256 i = prazo; i < mesesDecorridos; i++)
            {
                uint256 jurosMoratorios = percentualjuros;
                valorAtual = valorAtual + ((valorAtual*jurosMoratorios)/100);
            }
            
            valorAtual = valorAtual + ((valorAtual*multa)/100);
        }
        
        valor = valorAtual;
    }
    
    // Função que simula um plano de parcelamento de dívida, retornando o valor de cada parcela e o total.
    function simulaPlanoParcelamento (uint256 mesesDecorridos, uint256 parcelas) public view returns (uint256 parcela, uint256 totalParcela)
    {
        // Tempo mínimo para parcelar a dívida será de 6 meses após o vencimento da dívida.
        // Deverá atualizar o valor com os juros, caso contrário tomará como parâmetro valor = 0 (var valorBase) e não conseguirá simular.
        require (mesesDecorridos > prazo + 6, "Impossível simular parcelamento de dívida, fora do tempo mínimo.");
        
        uint256 jurosParcela = percentualjuros;
        parcela = valor / parcelas;
        parcela = parcela + ((valor*jurosParcela)/100);
        totalParcela = parcela*parcelas;
        return (parcela, totalParcela);
    }
        
}
