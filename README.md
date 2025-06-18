# JavaProvinha
Main:
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        VeiculoService service = new VeiculoService();
        int opcao;

        do {
            System.out.println("\n--- Menu ---");
            System.out.println("1 - Cadastrar veículo");
            System.out.println("2 - Listar veículos");
            System.out.println("3 - Atualizar veículo");
            System.out.println("4 - Remover veículo");
            System.out.println("5 - Realizar manutenção");
            System.out.println("6 - Sair");
            System.out.print("Opção: ");

            try {
                opcao = Integer.parseInt(scanner.nextLine());

                switch (opcao) {
                    case 1 -> {
                        System.out.print("Tipo (Carro/Moto): ");
                        String tipo = scanner.nextLine();
                        System.out.print("Placa: ");
                        String placa = scanner.nextLine();
                        System.out.print("Marca: ");
                        String marca = scanner.nextLine();
                        System.out.print("Modelo: ");
                        String modelo = scanner.nextLine();
                        service.cadastrar(new Veiculo(tipo, placa, marca, modelo));
                    }
                    case 2 -> service.listar();
                    case 3 -> {
                        System.out.print("Placa: ");
                        String placa = scanner.nextLine();
                        System.out.print("Nova marca: ");
                        String marca = scanner.nextLine();
                        System.out.print("Novo modelo: ");
                        String modelo = scanner.nextLine();
                        service.atualizar(placa, marca, modelo);
                    }
                    case 4 -> {
                        System.out.print("Placa: ");
                        String placa = scanner.nextLine();
                        service.remover(placa);
                    }
                    case 5 -> {
                        System.out.print("Placa: ");
                        String placa = scanner.nextLine();
                        service.manutencao(placa);
                    }
                    case 6 -> System.out.println("Saindo...");
                    default -> System.out.println("Opção inválida.");
                }
            } catch (Exception e) {
                System.out.println("Erro: " + e.getMessage());
                opcao = 0;
            }
        } while (opcao != 6);
    }
}

Veiculo.Java:
public class Veiculo {
    private String tipo;
    private String placa;
    private String marca;
    private String modelo;

    public Veiculo(String tipo, String placa, String marca, String modelo) {
        this.tipo = tipo;
        this.placa = placa;
        this.marca = marca;
        this.modelo = modelo;
    }

    public void exibir() {
        System.out.println(tipo + " - Placa: " + placa + ", Marca: " + marca + ", Modelo: " + modelo);
    }

    public void manutencao() {
        System.out.println("Realizando manutenção no " + tipo.toLowerCase() + " de placa " + placa);
    }


VeiculoService.java
import java.io.*;
import java.util.ArrayList;

public class VeiculoService {
    private ArrayList<Veiculo> veiculos = new ArrayList<>();
    private final String arquivo = "veiculos.txt";

    public VeiculoService() {
        carregar();
    }

    public void cadastrar(Veiculo v) throws Exception {
        if (buscarPorPlaca(v.getPlaca()) != null) {
            throw new Exception("Placa já cadastrada.");
        }
        veiculos.add(v);
        salvar();
        System.out.println("Veículo cadastrado com sucesso.");
    }

    public void listar() {
        if (veiculos.isEmpty()) {
            System.out.println("Nenhum veículo cadastrado.");
        } else {
            for (Veiculo v : veiculos) {
                v.exibir();
            }
        }
    }

    public void atualizar(String placa, String novaMarca, String novoModelo) throws Exception {
        Veiculo v = buscarPorPlaca(placa);
        if (v == null) throw new Exception("Veículo não encontrado.");
        v.setMarca(novaMarca);
        v.setModelo(novoModelo);
        salvar();
        System.out.println("Veículo atualizado com sucesso.");
    }

    public void remover(String placa) throws Exception {
        Veiculo v = buscarPorPlaca(placa);
        if (v == null) throw new Exception("Veículo não encontrado.");
        veiculos.remove(v);
        salvar();
        System.out.println("Veículo removido com sucesso.");
    }

    public void manutencao(String placa) throws Exception {
        Veiculo v = buscarPorPlaca(placa);
        if (v == null) throw new Exception("Veículo não encontrado.");
        v.manutencao();
    }

    private Veiculo buscarPorPlaca(String placa) {
        for (Veiculo v : veiculos) {
            if (v.getPlaca().equalsIgnoreCase(placa)) {
                return v;
            }
        }
        return null;
    }

    private void salvar() {
        try (BufferedWriter bw = new BufferedWriter(new FileWriter(arquivo))) {
            for (Veiculo v : veiculos) {
                bw.write(v.getTipo() + ";" + v.getPlaca() + ";" + v.getMarca() + ";" + v.getModelo());
                bw.newLine();
            }
        } catch (IOException e) {
            System.out.println("Erro ao salvar: " + e.getMessage());
        }
    }

    private void carregar() {
        File file = new File(arquivo);
        if (!file.exists()) return;

        try (BufferedReader br = new BufferedReader(new FileReader(arquivo))) {
            String linha;
            while ((linha = br.readLine()) != null) {
                String[] p = linha.split(";");
                if (p.length == 4) {
                    veiculos.add(new Veiculo(p[0], p[1], p[2], p[3]));
                }
            }
        } catch (IOException e) {
            System.out.println("Erro ao carregar: " + e.getMessage());
        }
    }
}
    // Getters e Setters
    public String getTipo() {
        return tipo;
    }

    public String getPlaca() {
        return placa;
    }

    public String getMarca() {
        return marca;
    }

    public String getModelo() {
        return modelo;
    }

    public void setMarca(String marca) {
        this.marca = marca;
    }

    public void setModelo(String modelo) {
        this.modelo = modelo;
    }
}
